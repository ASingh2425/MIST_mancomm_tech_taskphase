# ASSEMBLY LANGUAGE FOR REVERSE ENGINEERING
## Q1. Write an assembly program (x86) that adds 2 arbitary numbers, and prints to the console. 
'''
; add_two.asm – x86-64 Linux, NASM

default rel

section .data
    num1    dq 123456
    num2    dq 7890
    fmt     db "Sum = %ld", 10, 0   ; "Sum = %ld\n"

section .text
    global  main
    extern  printf

main:
    ; function prologue
    push    rbp
    mov     rbp, rsp

    ; sum = num1 + num2
    mov     rax, [rel num1]
    add     rax, [rel num2]

    ; printf("Sum = %ld\n", sum);
    lea     rdi, [rel fmt]     ; 1st arg: pointer to format string
    mov     rsi, rax           ; 2nd arg: sum
    xor     eax, eax           ; required before calling printf (variadic)
    call    printf

    ; return 0 from main
    mov     eax, 0

    ; function epilogue
    mov     rsp, rbp
    pop     rbp
    ret
'''    
<img width="1919" height="1144" alt="Screenshot 2025-12-08 004501" src="https://github.com/user-attachments/assets/93a75751-e88e-4a40-b958-f4fa77caf8e0" />

<img width="1916" height="354" alt="Screenshot 2025-12-08 004609" src="https://github.com/user-attachments/assets/3bb33c5b-71c1-489c-888d-ac15a11e7956" />


## Q2. Write an assembly program that takes a string as input from STDIN, and capitalizes all characters.
'''
default rel

section .bss
    buffer  resb 256        ; input buffer (max 256 bytes)

section .text
    global _start

_start:

    ; ---------------------------
    ; 1) Read input from STDIN
    ;    ssize_t n = read(0, buffer, 256);
    ; ---------------------------
    mov     rax, 0          ; syscall: read
    mov     rdi, 0          ; fd = STDIN (0)
    mov     rsi, buffer     ; buffer address
    mov     rdx, 256        ; max bytes to read
    syscall                 ; on return: RAX = number of bytes read

    mov     r8, rax         ; save length in r8 (for later write)
    mov     rcx, rax        ; loop counter = length
    mov     rbx, buffer     ; rbx = current pointer into buffer

    ; ---------------------------
    ; 2) Capitalize loop
    ; ---------------------------
capitalize_loop:
    cmp     rcx, 0
    je      print_result     ; if length == 0, done

    mov     al, [rbx]        ; load current char into AL

    ; if 'a' <= al <= 'z', convert to uppercase
    cmp     al, 'a'
    jb      skip_uppercase   ; if al < 'a', skip
    cmp     al, 'z'
    ja      skip_uppercase   ; if al > 'z', skip

    sub     al, 32           ; 'a'..'z' → 'A'..'Z'
skip_uppercase:
    mov     [rbx], al        ; store modified (or original) char

    inc     rbx              ; next byte
    dec     rcx              ; one less to process
    jmp     capitalize_loop

    ; ---------------------------
    ; 3) Write capitalized string to STDOUT
    ;    write(1, buffer, n);
    ; ---------------------------
print_result:
    mov     rax, 1           ; syscall: write
    mov     rdi, 1           ; fd = STDOUT (1)
    mov     rsi, buffer      ; pointer to buffer
    mov     rdx, r8          ; number of bytes to write (saved length)
    syscall

    ; ---------------------------
    ; 4) Exit(0)
    ; ---------------------------
    mov     rax, 60          ; syscall: exit
    xor     rdi, rdi         ; status = 0
    syscall

<img width="1919" height="1137" alt="Screenshot 2025-12-08 012233" src="https://github.com/user-attachments/assets/352fe40f-7450-46d2-9f16-82534a40c52d" />
<img width="1919" height="1141" alt="Screenshot 2025-12-08 012254" src="https://github.com/user-attachments/assets/77239fc9-fa0b-4ac5-a6aa-56002da78d18" />
<img width="1919" height="759" alt="Screenshot 2025-12-08 012154" src="https://github.com/user-attachments/assets/fbe62183-a5a3-4153-b1ca-24efeb3961b5" />
