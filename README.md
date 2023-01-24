# DAuth - Generate and destroy user accounts just-in-time

![image](https://user-images.githubusercontent.com/67427045/214201859-4318014e-9ed1-40a7-ac8a-a44269d0ec7f.png)<br>

This repo introduces authentication WITHOUT A USER DATABASE or user/password storage and/or exchange.<br><br>
The idea is to log into a website via exchanging a token (QR Code) which is generated by a combination of name and passphrase.<br>
The name and passphrase will be salted and then hashed. The result is feed to a QR Code generator.<br>
<br>
This way the QR Code becomes the public key. If the QR Code gets decrypted, the result is just the hash of the salted name and passphrase.(useless) Only the owner of the name and the passphrase can re-create the exact same QR Code. (inside the app)<br>

# How does it work?

The component combines both inputs, name and pass. The QR Code is generated from the result (namepass)<br>
The exact same QR Code pattern is only generated again, with the right name and passphrase.<br>

To do:
- salt
- hash
- hide or show the QR code
- QR Code and/or hash match function

# How to use?

- npm install (install dependencies)
- npm run dev (run hot-reload dev environment)
- npm run build (edge-ready, vanillaJS, compressed)

# Sequence

1. At sign-up you generate a new QR Code(hash) from your name and a passphrase.<br><br>

2. The QR Code(hash) is for the period of the session connected with the user events/actions.

3. The first time you create a profile or create a post, this QR Code(hash) will be connected with your new profile or post<br>
```profile-creator: qrcode123 or post-creator: qrcode123``` (Pseudocode)<br><br>

This way, the profile or post "knows" who you are (which gives permission for edit/delete for instance)

```
// Pseudocode

{#if user = qrcode123}
editmode()

{#else}
watchmode()

{/if}
```

4. If the user logs out, the session gets destroyed, but not the created documents (profile, posts, messages)(own storage&sync-logic neccessary)
5. If the user returns, with the right combination of name and passphrase, he generates the exact same QR Code(hash). This gives him access to his created documents again.

# Advantages

- Your name and passphrase are neither locally nor online saved. Just in your head.
- No user-database necessary. The user basically generates himself on-demand, by simply logging in. And destroys himself by logging out.
- No database requests for permissions. The moment you load the post your permissions are set.

# How to improve security? (suggestions, Work in Progress)

- by hiding the QR-code
- https TLS! (against man in the middle attack)
- salting
- hash (SHA-3)
- encryption (AES256)
- encrypted transfer of the hash
- encrypted transfer of the QR Code (base64, webcrypto, other crypto-lib, SEA)
- instead of storing and or sending the QR Code you can just take the hash and generate the QR Code client-side again (also good for avatar or friend-connect for instance)
- mechanism to make sure only 1 QR-code each user
<br><br>

![image](https://user-images.githubusercontent.com/67427045/213913807-464d737b-0bfb-4ece-a0d4-64cceac29671.png)
