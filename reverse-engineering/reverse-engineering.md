## picoCTF Safe Opener 2

We are given a .class file. It is a compiled Java file. We plug it into an online Java decompiler, and get the flag.

## picoCTF Reverse

Run strings on it, grep for 'pico' and it pops right up.

## picoCTF Timer

This one was a little tricky for me. I eventually end up using apktool on it 

```apktool d timer.apk```

This creates a directory with information on the apk. I enter the directory, and rather than looking for everything, I grep for 'pico' in the directory contents.

```grep -r 'picoctf'```

Yields the flag.

## picoCTF Ready Gladiator 0

This marks a series of three challenges based on the game "core wars". In core wars, you think of the game as two programs "fighting" each other.

The goal here is to make a warrior that always loses. To do this, we simply change the "1" in the imp.red program they give us, to a 0. This causes the other program to "win" in the context of core wars rules. Why? Because of the way the imp works - it is moving and copying itself. Our program isn't.

## picoCTF Ready Gladiator 1

Now, we need to actually make something that wins. If you do some research onto "core wars", you get into a rabbit hole. It's an interesting strategy game, with a lot of depth. More importantly, there's a lot of dicussion on it. The "imp.red" we are given is actually a classical warrior in the context of the game. As such, there is documentation on beating it.

We only need to win once for this challenge.

https://corewar-docs.readthedocs.io/en/latest/corewar/strategies/

The above link contains some basic strategies for the game, along with the corresponding templates. I grab one: the dwarf. I copy and paste it into a file to use in the picoCTF instance.

```
;redcode
;name dwarf
;assert 1
start   add.ab  #4, bmb
        mov.i   bmb, @bmb
        jmp     start
bmb     dat     #0, #0
end
```

This "dwarf" will defeat the other program, the "imp", and yield us the victory, and the flag. 

## picoCTF Ready Gladiator 2

This is the third and final Core Wars challenge. It's the same concept as the above two, but this time, we need to create an undeafable program, that will win every single battle against the imp - 100 times.

Funnily enough, there's a reddit post on this exact subject - creating a program that will beat the imp every time. Our warrior needs to look like this.

```
;redcode
;name predator
;assert 1
start   JMP 0, <-5
end

```

The way this works is by messing around with the memory addresses, such that when the imp's program attempts to execute, it simply fails. This will beat the imp every time, and yield us the flag.