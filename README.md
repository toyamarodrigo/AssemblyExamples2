# Assembly x8086 Example
## Excercise

Print a text up to 50 characters or finished with $ that prints the original text, in uppercase and the first letter in uppercase.

```asm
Text: hello

OUTPUT:

Original text: hello
Uppercase: HELLO
First Uppercase: Hello
```

```asm
.8086
.MODEL SMALL
.STACK 100H
.DATA

	BANNER1 DB 0DH, 0AH, "Enter Text: $"
	BANNER2 DB 0DH, 0AH, "Original Text: $"
	BANNER3 DB 0DH, 0AH, "Text in Uppercase: $"
	BANNER4 DB 0DH, 0AH, "First Letter Uppercase: $"
	TEXT DB 50 DUP('$'),0DH, 0AH, "$"
	TEXT2 DB 50 DUP('$'),0DH, 0AH, "$"

.CODE
MAIN PROC
	MOV AX, @DATA
	MOV DS, AX
	;i = 0
	MOV BX, 0

	;Prints BANNER1
	MOV AH, 9
	MOV DX, OFFSET BANNER1
	INT 21H

CICLE:
	;Input AL
	MOV AH, 1
	INT 21H
	;if AL = $ -> KEEP
	CMP AL, '$'
	JE KEEP

	;Copy letter AL in TEXT[i]
	MOV TEXT[BX], AL
	;i++
	INC BX

	;if i = 50 -> KEEP, ifnot CICLE
	CMP BX, 50
	JE KEEP
	JMP CICLE
KEEP:
	;Prints BANNER2
	MOV AH, 9
	MOV DX, OFFSET BANNER2
	INT 21H

	;Prints TEXT
	MOV AH, 9
	MOV DX, OFFSET TEXT
	INT 21H
	
	;i = 0
	MOV BX, 0
RETURN:
	;Letter AL to TEXT[i]
	MOV AL, TEXT[BX]
	;if AL = $ -> UPPERCASE
	CMP AL, '$'
	JE UPPERCASE

	;if AL < 'a' -> COPYAL
	CMP AL, 'a'
	JL COPYAL

	;if AL > 'z' -> COPYAL
	CMP AL, 'z'
	JG COPYAL

	;Converts in uppercase (AL - 20hex[32dec])
	SUB AL, 20H

COPYAL:
	;Letter AL to TEXT2[i]
	MOV TEXT2[BX], AL
	
	;i++
	INC BX
	JMP RETURN
UPPERCASE:
	;Prints BANNER3
	MOV AH, 9
	MOV DX, OFFSET BANNER3
	INT 21H

	;Prints TEXT2
	MOV DX, OFFSET TEXT2
	MOV AH, 9
	INT 21H
	;i = 0
	MOV BX, 0
AGAIN:
	;Letter TEXT[i] to AL
	MOV AL, TEXT[BX]
	;if AL = '$' -> FIN
	CMP AL, '$'
	JE FIN

	;if AL < 'a' -> OTHER
	CMP AL, 'a'
	JL OTHER

	;if AL > 'z' -> OTHER
	CMP AL, 'z'
	JG OTHER

	;Converts to Uppercase (AL - 20hex[32dec])
	SUB AL, 20H 
OTHER:
	;Letter AL to TEXT2[i]
	MOV TEXT2[BX], AL
	;i++
	INC BX
OTHER2:
	;Letter TEXT[i] to AL
	MOV AL, TEXT[BX]

	;if AL = '$' -> FIN
	CMP AL, '$'
	JE FIN

	;if AL < 'A' -> LEAVE 
	CMP AL, 'A'
	JL LEAVE1

	;Si AL > 'Z' -> LEAVE
	CMP AL, 'Z'
	JG LEAVE1

	;Converts to Lowercase (AL + 20hex[32dec])
	ADD AL, 20H
LEAVE1:
	;Letter AL to TEXT2[i]
	MOV TEXT2[BX], AL

	;i++
	INC BX
	;Letter TEXT[i] to AL
	MOV AL, TEXT[BX]

	;if AL = 20H (32decimal) 
	CMP AL, 20H
	JE COPY

	JMP OTHER2
COPY:
	;Letter AL to TEXT2[i]
	MOV TEXT2[BX], AL
	;i++
	INC BX
	JMP AGAIN
FIN:
	;Prints BANNER4
	MOV AH, 9
	MOV DX, OFFSET BANNER4
	INT 21H
	
	;Prints TEXT2
	MOV AH, 9
	MOV DX, OFFSET TEXT2
	INT 21H
	;FIN
	MOV AX, 4C00H
	INT 21H
	ENDP
END MAIN
```