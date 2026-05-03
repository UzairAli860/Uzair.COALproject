# Uzair.COALproject
about coal project
INCLUDE Irvine32.inc

.data
inputMsg    BYTE "Enter Password: ",0
weakMsg     BYTE "Password Strength: WEAK",0
mediumMsg   BYTE "Password Strength: MEDIUM",0
strongMsg   BYTE "Password Strength: STRONG",0

password    BYTE 50 DUP(0)
len         DWORD ?

.code
main PROC

    ; Display input message
    mov edx, OFFSET inputMsg
    call WriteString

    ; Read password
    mov edx, OFFSET password
    mov ecx, 50
    call ReadString
    mov len, eax        ; password length

    ; Clear flag registers
    xor ebx, ebx        ; BL = upper, BH = lower
    xor ecx, ecx        ; CL = digit, CH = special

    mov esi, OFFSET password
    mov edi, len        ; counter

checkLoop:
    mov al, [esi]

    ; Check Uppercase A-Z
    cmp al, 'A'
    jb checkLower
    cmp al, 'Z'
    ja checkLower
    mov bl, 1
    jmp nextChar

checkLower:
    ; Check Lowercase a-z
    cmp al, 'a'
    jb checkDigit
    cmp al, 'z'
    ja checkDigit
    mov bh, 1
    jmp nextChar

checkDigit:
    ; Check Digit 0-9
    cmp al, '0'
    jb checkSpecial
    cmp al, '9'
    ja checkSpecial
    mov cl, 1
    jmp nextChar

checkSpecial:
    ; Special character
    mov ch, 1

nextChar:
    inc esi
    dec edi
    jnz checkLoop

    ; Count satisfied conditions
    mov eax, 0
    add al, bl
    add al, bh
    add al, cl
    add al, ch

    ; Decide password strength
    cmp len, 10
    jbe weak

    cmp al, 4
    je strong

    cmp al, 2
    jae medium

weak:
    mov edx, OFFSET weakMsg
    call WriteString
    jmp endProgram

medium:
    mov edx, OFFSET mediumMsg
    call WriteString
    jmp endProgram

strong:
    mov edx, OFFSET strongMsg
    call WriteString

endProgram:
    call Crlf
    exit

main ENDP
END main
