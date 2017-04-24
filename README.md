# DeCryKe

Deniable Cryptographic Key

DeCryKe is a technique to transform almost any cryptographic algorithm into [deniable-one](https://en.wikipedia.org/wiki/Deniable_encryption), it is a decry of [Russia's anti-terrorism law](https://www.theguardian.com/world/2016/jun/26/russia-passes-big-brother-anti-terror-laws) (a.k.a. Yarovaya law) and its [FSB implementation](https://translate.google.com/translate?hl=en&sl=auto&tl=en&u=http%3A%2F%2Fpublication.pravo.gov.ru%2FDocument%2FView%2F0001201608120037).

As you might know, now in Russia you are forced to provide the meanings (keys) to decode (decrypt) all the communication and data, othervise you will be punished.

It concerns all messengers, websites, emails, social networks, ISPs, etc.

Nonetheless, this law can be easy workarounded, as it was announced.

## How it works

In cryptography, deniable encryption is the incapacity to prove that a particular data was encrypted, thus the fact of encryption can be denied. Although it is an easy task for Vernam cipher, it is not for modern ciphers (like AES), regardless all efforts (like chaffing and steganography).

A quick example:

1. Alice encrypts the true message (attack at dawn) with the true key (qwerty) and the decoy message (how are you) with the decoy key (123456) all in one message  and sends it to Bob.

2. Bob uses the true key on the received message to reveal the true message.

3. Eve demands to decrypt the communication, Bob and Alice reveal the decoy key for the decoy message, but the fact that there is another key with a possible message remains secret.

Deniable Cryptographic Key focuses on key, rather than encryption algorithm:

1. Alice encrypts the true message (attack at dawn) with the true key (qwerty) and the decoy message (how are you) with the encrypted true message using the true key and sends the encrypted decoy message to Bob.

2. Bob uses the true key to decrypt the key for the decoy message (synchronized with Alice), revealing the true message.

3. Eve demands to decrypt the communication, Bob and Alice reveal the encrypted true message as the key for the decoy message, but the fact that the key it-self is encrypted remains secret.

## Analysis

Since the key can be random-like as well as the encrypted data, there is no distinguish between the two and we can use one in the place of another, thus the plausible deniability and total secrecy are achieved.

This technique is applicable for almost any encryption algorithm (especially Russian GOSTs, although some asymmetric algorithms (like RSA) may not work) and is particularly strong in case of Vernam cipher, the following prove of concept (PoC) uses AES-256 in CBC mode and does not contain any particular code since, pretty every cryptographic tool is compatible with this technique.

Moreover, it is possible to use it in emails, websites and even real-time messengers. An idea is to transmit the decoy messages and to store the encrypted true messages in a cloud storage.

This can also protect from rubber-hose cryptanalysis, if one does not possess the tool during the "process", stores the true keys in a secure place and deletes all the trace/logs/evidence of such activity, also the encrypted true message should not exceed cipher's block size (32 bytes for AES-256-CBC), in order not to be suspicious.

### PoC How to

For Linux:
```bash
echo 'Normally, nothing is to be installed, but just in case :' && sudo apt-get install openssl || echo 'use an appropriate package manager or/and OpenSSL alternatives'
```

Fow Windows, you will need [OpenSSL](http://downloads.sourceforge.net/gnuwin32/openssl-0.9.8h-1-setup.exe).

Key Encryption:
```bash
openssl aes-256-cbc -pass file:True_Key -in True_Message -out True_Message.enc -nosalt
```

Replace 'openssl' by '& "C:\Program Files\GnuWin32\bin\openssl.exe"' for Windows.

Remove '-pass file:True_Key' to provide an interactive passphrase.

It will produce True_Message.enc that is to be revealed in case of demand, as the key.

The True_Message should be securely erased/destroyed (both [Windows](http://www.makeuseof.com/tag/securely-delete-files-hdd-ssd-windows/) and [Linux](http://askubuntu.com/questions/57572/how-to-delete-files-in-secure-manner)) and the True_Key should be kept in a secure place or remembered and destroyed/erased securely.

Don't forget to clean all the logs for this operation ([Windows](http://www.wikihow.com/Delete-Run-History-in-Windows) and [Linux](http://superuser.com/questions/384366/remove-a-certain-line-from-bash-history-file)) and it's even better to spoof/decoy it as a "secure random key generation" (by renaming the command to something like "Get-Random > Secure_Key" for Windows and "dd if=/dev/urandom bs=1M count=1 of=Secure_Key" for Linux or "openssl rand 32 > Secure_Key" for both) since, you "suppose to" encrypt your message with it.

Of course, the key (or the passphrase) should be really [strong](https://www.youtube.com/watch?v=IPphyjkXnPc).

Encryption:
```bash
openssl aes-256-cbc -pass file:True_Message.enc -in Decoy_Message -out Decoy_Message.enc
```

It will produce Decoy_Message.enc that is to be sent alongside with the True_Message.enc as the key for decryption (or stored in an insecure place).

Decryption:
```bash
openssl aes-256-cbc -pass file:True_Key -in True_Message.enc -out True_Message -nosalt -d
```
It will produce True_Message, revealing it.

Don't forget to wipe the trace as mentioned for key encryption.

If someone demands you the key, assuming that they have your transmission (Decoy_Message.enc using eavesdropping for instance), you just provide True_Message.enc as the key, so it will decrypt the decoy being sent:
```bash
openssl aes-256-cbc -pass file:True_Message.enc -in Decoy_Message.enc -out Decoy_Message -d
```

Preferably, the receiver should read Decoy_Message as well and answer using the same technique, so both sides maintain a double conversation (a real-one and a fake-one).

#### Notes

Cryptography is power and power is freedom.

As long as Cypherpunk exists, the freedom will.

