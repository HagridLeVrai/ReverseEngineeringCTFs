# ASSEMBLY TUTO

Assembly tutorial that we did at the start of the workshop

## 01) Return 42

Start by saving the old stackframe by saving ebp address in the stack.

```asm
push ebp
```

 Then create a new stackframe.

```asm
mov ebp, esp
```

Because the `EAX` register stores the functions return code, we are going to assign the value `42` to `EAX`.

```asm
mov eax, 42
```

Finally, restore the old stackframe before returning.

```asm
pop ebp
ret
```

## 02) Addition

First we need to get our 2 arguments. We can do so by retreiving the `esp+8` value for the first argument and `esp+12` for the second.
It is their adress in the stack. 12 because it is after the `argn` and `path` values (every 4 bits).

```asm
mov edx, [ebp+8]
mov eax, [ebp+12]
```

Let's add those two values !

```asm
add eax, edx
```

(eax is returned as the function's return value)

# CTFs

Multiple Blackfoot's reverse engineering challenges

## ELF  

### ELF_01

Using the graph viewer in Cutter, I could see that the binary was asking for an argument and then comparing this argument to a string.

The string was `Thiswasreallyeasy`. If the user input was equal to this string, the binary was printing `Yes this is correct`.

I guessed that this was the flag.

### ELF_02

The program start by verifying the number of arguments. If not 1, it exit.

Then, when 1 argument is entered, it will check its lentgh. If it's not 14, it will exit.

Finally, the program will take the 14 char string entered by the user and substract 1 to every letter. The result is compared to the password (`password424242`). This password is in one of the str at the start of the program. If it's not the same, it will exit.

We can bypass this by entering `password424242` as our argument 1 but we need to remove 1 byte to every character.

```text
o`rrvnqc313131
```

That is our flag.

### ELF_03

Le programme commence par vérifier que l'input est d'une longueur de 16 charactères (strlen de 16).

Ensuite il execute une fonction nommé `check_pw` qui prends 3 arguments.

Grâce au décompilateur et au hexdump, j'ai pu trouver que la fonction fonctionne comme ceci:

- arg1 = `RDI` = `input`
- arg2 = `RSI` = `MasKerade133742A`
- arg3 = `RCX` = `02030203050000000000000000000000` (hex)

```c
check_pw(arg3, arg2, arg1) {
    while (arg2 + ctr != NULL) {
        edx = arg3 + ctr
        dl += arg2 + ctr
        if (arg1 + ctr != dl) {
            ret 0
        }
        if (arg2+ ctr + 1 == NULL) {
            ret 1
        }
        ctr++
    }
    ret 1
}
```

En gros, elle cheque que l'input de l'utilisateur est égale à `MasKerade133742A` + `02030203050000000000000000000000`.
Elle retournera donc 1. Dans le main, si cette fonction retourne 1, c'est un succès.

J'ai donc ajouter `RCX` à mon input pour matcher ce que le programme attendait.

Ce qui donne `OduNjrade133742A`. J'ai donc pu obtenir mong flag.

### ELF04

Doit avoir 1 argument.

edi = 0x10

edi est la longueur de l'argument 1.

ecx = 0x680

Est la valeur en hex de l'addition des tous les charactères de l'argument 1 (h + h + h + ...).

L'argument `hhhhhhhhhhhhhhhh` était correct, j'ai donc validé mon flag avec l'url et j'ai obtenu `BFS[ApideliveredFLAG]`.

## QT

### QT1

I found the flag by looking at every str in Cutter with the top bar.

I found a str containing `BFS["youpownednotebook"]` at the adress `0x0000c472`, that is the flag.


## Wasm

### ez_breezy

I found the flag by opening my browser's dev tools and opening `index.wasm` in the Sources.

It was at the adress `0x01a3`. (It just looked like a flag/password so I just guessed it was  the flag x) )

### kill_la_kool

I understood that you need to complete the password `!0#{W}R7_s30u__?_nyd` by replacing all of the `_` with a character.

Those character can be found with 4 strings that are in the code.

- `a k"`
- `qErE`
- `A j$`
- `kApq"`

The program is using `strlen` and `strcmp`.
I could not find the solution in time.
