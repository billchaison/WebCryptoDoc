# WebCryptoDoc
Using the Web Crypto API in stand-alone secure HTML documents.

This is a POC demonstrating how to create an HTML file with embedded secure content that can be decypted by someone with access to the key material.

Example file SecureDoc.html<br />
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>AES-256-CTR Document</title>
<script>
const Hex2Array = (hexString) => Uint8Array.from(hexString.match(/.{1,2}/g).map((byte) => parseInt(byte, 16)));
async function Recover_CT() {
   var keyin = document.getElementById("0xkey").value;
   var ivin = document.getElementById("0xiv").value;
   if(keyin == "" || ivin == "") {
      alert("Bad Key or IV supplied");
   } else {
      var keyarr = Hex2Array(keyin);
      var ivarr = Hex2Array(ivin);
      if(keyarr.length != 32 || ivarr.length != 16) {
         alert("Bad Key or IV supplied");
      } else {
         const key = await crypto.subtle.importKey("raw", keyarr, {name: "AES-CTR", length: 256}, false, ["decrypt"]);
         const iv = ivarr;
         var cthex = document.getElementById("encrypted").textContent;
         cthex = cthex.replace(/\n/g, '');
         var ct = Hex2Array(cthex);
         const pt = await crypto.subtle.decrypt({name: "AES-CTR", counter: iv, length: 128}, key, ct);
         var td = new TextDecoder();
         var msgdec = td.decode(new Uint8Array(pt));
         document.getElementById("output").innerHTML = msgdec;
         document.getElementById("dec").disabled = true;
         document.getElementById("0xkey").value = "";
         document.getElementById("0xiv").value = "";
      }
   }
}
</script>
</head>
<body>
<font face="arial">
This document utilizes the Web Crypto API to decrypt embedded cipher text in<br>
your browser.  It is a POC for sharing stand-alone secure documents.<br><br>
The decryption Key and IV for this document can be found in your password<br>
manager by logging in <a href="https://my.pam.local?secret=77c5pqt9" target="_blank">here</a>.<br><br>
Enter the Key (64 hex chars):&nbsp;<input type="text" id="0xkey" size="70" maxlength="64"><br>
Enter the IV (32 hex chars):&nbsp;<input type="text" id="0xiv" size="38" maxlength="32"><br>
<button type="button" id="dec" onclick="Recover_CT()">Decrypt</button>
</font>
<hr>
<!--
   Generate the encrypted data using openssl:

   cat doc.html | openssl enc -aes-256-ctr -nosalt -K [key here] -iv [iv here] | xxd -p
-->
<div id="encrypted" style="display: none; visibility: hidden">
11eaf4c0d35b457910f7f0dc7bb5d90dd42e13260fff9a3b522ef39b38f6
be95f9ddfce9b57edb06902463dee588e6989232c2b65a924c5f937dab76
bcbce1b7d43d512c3bc572bb28a88c6f58e47f33de448f032098ef964396
967b786b26c0b5ce36ab1c0286c522f90788df4da7e040de9e347648e5d7
27dde08e703f79ae2a02419be2077d71285ade4d7f53f3af1f006c15ed54
8edd790f5ab7dbde248ae69ec3aa75475871fd0a0073c93f0a620ec2646e
a1f3efd54ee1f368f2f0e159abb57da4260459bd459ce39c356d52cec7fa
ead33805c106ea17c3bba2cabcfc4bb5b6117291b7b7baa3f52284574a51
08f33c780206220535d4360f8555a958b6bdaf0a248d902c2b281f818764
fae69d92b6339bedf077d1d95d7e88a81ece0cd58059ea81ef596a417faa
281a7ba49fb49fba3f4d0e42a859bf4936a92440bb61e8fa9f1d42cbfe67
98748a0870a550cca7708054e793f5372603a390b738e8e56788c450be83
7f417f4b62747caf1a1fb5de211cec897bfb17326f11943063db4f504cba
9a76c95273c559bddd55993816fa1872d5f49c67e282d83ba729e0c5a7e5
58e44728779c7a267cf1e54db1fd3abd4975b5a0bf3749bbe907a47dc714
7348fdd2b36af13880db5e27face946ddf958eaa1f5bab79ccca3c5db8d4
ca50d5536a817d21ec5eafef9d9eeea227dfa4153a6ac254c27818b92a90
2c5ff10f5bb77919f75c81c63a1e6c7991af992f64c0fa9cb38f03a76dba
77fd0ebe10b526b89d6781e108fa3c174c39b5cda098cd460c737a577115
b5443ec20eade0eb6b53c6889e738ef9b0132203333e48689f7d69038e9e
5a462f77d55b16
</div>
<div id="output">Decrypted text shows up here</div>
</body>
</html>
```

The Key in this example is `b195eba6be15c1c0a014ca3315d4930a132d8eb2f17ac310c6c09a7e63aa57cd`<br />
The IV in this example is `c4a62ca6e857a73eaef60e9bd380d41e`
