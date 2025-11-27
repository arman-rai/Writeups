```
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack nginx
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
|_http-favicon: Unknown favicon MD5: 20E95ACF205EBFDCB6D634B7440B0CEE
| http-methods:
|_  Supported Methods: GET
|_http-title: Hack The Box :: Penetration Testing Labs
|_http-trane-info: Problem with XML parsing of /evox/about
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

view-source:http://2million.htb/js/inviteapi.min.js
This code is a **packed and obfuscated JavaScript function** designed to hide its original, readable form.
```
function verifyInviteCode(code) {
  var formData = {
    "code": code
  };
  $.ajax({
    type: "POST",
    dataType: "json",
    data: formData,
    url: '/api/v1/invite/verify',
    success: function(response) {
      console.log(response)
    },
    error: function(response) {
      console.log(response)
    }
  })
}

function makeInviteCode() {
  $.ajax({
    type: "POST",
    dataType: "json",
    url: '/api/v1/invite/how/to/generate',
    success: function(response) {
      console.log(response)
    },
    error: function(response) {
      console.log(response)
    }
  })
}
```

invoked the function on console, got this:
```
makeInviteCode()  
undefined  
Object { 0: 200, success: 1, data: {…}, hint: "Data is encrypted ... We should probbably check the encryption type in order to decrypt it..." }
0: 200
data: Object { data: "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb /ncv/i1/vaivgr/trarengr", enctype: "ROT13" }
data: "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb /ncv/i1/vaivgr/trarengr"
enctype: "ROT13"
<prototype>: Object { … }
hint: "Data is encrypted ... We should probbably check the encryption type in order to decrypt it..."
success: 1
```

Sent a POST request and got this 
```
namura@pop-os ~/hackthebox $ curl http://2million.htb/api/v1/invite/how/to/generate
namura@pop-os ~/hackthebox $ curl -X POST http://2million.htb/api/v1/invite/how/to/generate
{"0":200,"success":1,"data":{"data":"Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb \/ncv\/i1\/vaivgr\/trarengr","enctype":"ROT13"},"hint":"Data is encrypted ... We should probbably check the encryption type in order to decrypt it..."}namura@pop-os ~/hackthebox $
namura@pop-os ~/hackthebox $ curl -X POST http://2million.htb\/api\/v1\/invite\/generate
{"0":200,"success":1,"data":{"code":"TkpFSkQtRk1WSVctM09IOU4tOThHMzc=","format":"encoded"}}namura@pop-os ~/hackthebox $

code: NJEJD-FMVIW-3OH9N-98G37
```

Got in using the code and entered using namura@2mil.htb:password
