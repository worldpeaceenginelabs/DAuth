# DAuth - Generate and destroy user accounts just-in-time

# ATTENTION! WORK IN PROGRESS!<br>DON'T USE FOR NOW!<br>EXPERIMENTAL - BUT MY ONLINE RESEARCH AND TALKS WITH CRYPTOGRAPHY EXPERTS IMPROVING DAuth EVERY DAY,<br>SO STAY TUNED...
<br>

![image](https://user-images.githubusercontent.com/67427045/215254130-c0a6d731-7086-4451-86ed-aaa4f9048035.png)<br>
<br>

# The basic idea
This repo introduces authentication WITHOUT A USER DATABASE or user/password storage and/or exchange.<br>
(i learned later that i am basically experimenting with a mix from signatures and zero knowledge proof)<br>
<br>
#### The idea is to create and identify a user by creating a signed public key just-in-time.<br>
A deterministic algorithm which creates the same signed public key from the same credentials every time the user drops his credentials into the DAuth Login.<br>
<br>
Only the owner of the right name and the passphrase can re-create the exact same signature again.<br>
So same signature = same user... But nothing to store!<br>

- The credentials (name, pass, ?) get salted then hashed. The private key gets salted and encrypted.<br>
With the private key, the user can sign and verify that he is the actual owner of that public key, without transfering or even storing his secret.

- The signed public key is then matched with the .get/.put(API) request of your app itself, so basicly it authenticates with the API limiter of your app and the document ownership(example in course of action) BTW: Its an auth for the JAMstack, so no servers, no workers, no NodeJS necessary(except the relays). Everything is stored static on edge, but dynamically consuming APIs. You can also use it with your typical whatever stack, it runs just everywhere.

- From the public key value an unencrypted QR Code is generated.<br>
If the QR Code gets decrypted, the result is the public key.<br>
This makes it easy to identify yourself to others, share contacts or exchange files ad-hoc. Maybe even pay with your public key or on-board a flight, who knows?<br>
<br>

#### A huge flaw of this method is that you can create a new account just by typing in a different name and password.
This is an invitiation for bs. A bot could fire a thousand account creations per minute. There has to be a minimum obstacle, like today we need one confirmed emailaddress or one confirmed mobile number each account for instance. (a small obstacle but it works)<br>
<br>

#### A possible solution would provide even more authentication security: A sign-up procedure + 2FA-TOTP<br>
##### Plot for Sign up with D-Auth
1. You just type in your name, your passphrase gets generated with cpu-noise randomness(30 digits alphanumeric)
2. A prompt will tell you to safe the generated pass-phrase in a password manager (i like the "just in your head" more, but better than 12345 passwords)
3. If done, the prompt will ask you to activate 2FA authentication, so connecting it with your authenticator app, BEFORE you get access to the API and / or the app.<br>(zero knowledge proof = we dont know WHO, but exactly that its THE SAME user again.)
4. And last step, you log in with your credentials from the password manager and 2FA. (as a confirmation step)

This way the sign-up is a bit more of work, distracts sign-up junkies and we have proof its you again, well, the owner of your Google authenticator app... (A friend just reminded me of the fact "users can switch devices")<br>

# How does it work?

### Here is a first try (Work in progress)
![image](https://user-images.githubusercontent.com/67427045/214812508-e0c8ee14-82cb-48df-a19e-9ce333af9543.png)
<br>

### Basic course of action
1. At sign-up you generate a signed public key from your name and a passphrase.(+ salts)<br><br>

2. The public key is for the period of the session connected with the user events/actions.

3. The first time you create a profile or create a post, this public key will be connected with your new profile or post<br>
```profile-creator: hash123 or post-creator: hash123``` (Pseudocode)<br><br>

This way, the profile or post "knows" who you are (which gives permission for edit/delete for instance)

```
// Pseudocode

{#if user = hash123}
editmode()

{#else}
watchmode()

{/if}
```

4. If the user logs out, the session gets destroyed, but not the created documents (profile, posts, messages)(own storage&sync-logic neccessary)

6. If the user returns, with the right combination of name and passphrase, he generates the exact same signed public key. This gives him access to his created documents again.

# Advantages

- Your name and passphrase are neither locally nor online saved. Just in your head.
- No user-database necessary. The user basically generates himself on-demand, by simply logging in. And destroys himself by logging out.
- No database requests for permissions. The moment you load the post your permissions are set.

# How to improve security? (suggestions, Work in Progress)

- by hiding the QR-code
- https TLS! (against man in the middle attack)
- salting
- hash
- encryption
- encrypted transfer of the hash
- encrypted transfer of the QR Code (base64, webcrypto, other crypto-lib, SEA)
- instead of storing and or sending the QR Code you can just take the hash and generate the QR Code client-side again (also good for avatar or friend-connect for instance)
- mechanism to make sure only 1 QR-code each user<br>
<br>

# Webcrypto API
https://gist.github.com/pedrouid/b4056fd1f754918ddae86b32cf7d803e (Web Cryptography API Examples - A collection of well commented, well ordered snippets for 20 algorithms)<br>
<br>
https://diafygi.github.io/webcrypto-examples/ (This table is live! Every ✓ or ✗ on this page is a test to see if your browser supports that method in WebCryptoAPI.)<br>
God bless the internet, open-source and collaboration. Amen 🙏😂<br>
<br>

# MUST read/watch
Fireship: https://youtu.be/NuyzuNBFWxQ (7 Cryptography Concepts EVERY Developer Should Know)<br>
<br>
Computerphile: https://youtu.be/8ZtInClXe1Q (How NOT to Store Passwords!) and https://youtu.be/b4b8ktEV4Bg (Hashing Algorithms and Security)<br>
<br>
https://stackoverflow.com/questions/549/the-definitive-guide-to-form-based-website-authentication/477578#477578 (The definitive guide to form-based website authentication)<br>
<br>

# NPM

- npm install (install dependencies)
- npm run dev (run hot-reload dev environment)
- npm run build (edge-ready, vanillaJS, compressed)
<br>

![image](https://user-images.githubusercontent.com/67427045/213913807-464d737b-0bfb-4ece-a0d4-64cceac29671.png)<br>

<br><br>

# The following slide is the actual state that every developer will face at some point when starting with GunJS (or any other decentralized database) (Github, Cloudflare, ENV's, are exchangeable with your providers/methods)
 
![image](https://user-images.githubusercontent.com/67427045/215068864-69e8a618-9df6-4278-acb1-56ffbfe0f579.png)

I started to look into the auth/spam issue from ground up, but this time more visually.
Plus I had some nice cryptography geek discussion on discord.

I invite everybody to wrap your head around the slide, to find the best balance between...

Authorized and unauthorized user (which share a red flag!)
- deny unauthorized user to post at all (red flag)
- authorized user spams or even wants to spam (red flag)
- authorized user posting to much or bs (yellow flag)
- authorized user posting (green flag)

...and how to measure, identify and regulate them.

Slide (duplicate to modify) https://docs.google.com/presentation/d/1xb6l41eqt6OYxNwtZJSh1wC_rTMrIEyExcsCBdhRIxo/edit?usp=sharing
 
I copy pasted the top slide to the bottom, made the ME card a user, and pretended to just lock the whole system. So i exchanged the red conditions with new green conditions.

Notice from the color change:

**WE CANT FULLY SECURE THE APP BECAUSE THE CODE IS MODIFIABLE**
- someone modifying your app's code seems always to be an issue, even if you basicly unplug your server.

**WE CANT FULLY SECURE THE RELAY EITHER, BECAUSE THE ADDRESS CAN BE KNOWN**

- Your app isn't even necessary, someone can just inject .put/.get on your infrastructures IPs/adresses. (Inject where? he will know from your sourcecode, the F12 console or some other tool) And be sure every peer, relay, home- cloud- or edge- server address can basically traced back by someone who really wants it. (this points us to using proxies to make it even harder to find our infrastructure!)

## THE FORMER POINTS US STRONGLY ON SECURING THE DATA ITSELF, THE HANDLING OF .get/.put (API)

#### Measurement No 1: ENCRYPTION (for data with audience less then absolute everybody)
In case of even someone legit or someone sneaking in, they only find garbage.
hash, padding, encryption: ECDSA-SALT-RSA or SHA-3, SHA256, AES

#### Measurement No 2: VALIDATION/SIGNING
Sign and validate all data with keypairs (transfer/post/message)
hash, padding, encryption: ECDSA-SALT-RSA, or HMAC, PBKDF2

#### Measurement No 3: RESTRICT/LIMIT/BALANCE ACCESS TO .get/.put (API)
- AUTH
- Common-Sense Request Limitations:
for instance, a couchsurf host can only offer 1 stay (send one hosting post) each day max...(common-sense)
- Requests in a timeframe (request-rate):<br>
...While a couch searcher can message to different hosts with a rate of<br> 
RULE-1:"not more than one per second" (=a few short messages in a row, but not spamming the other user(s)),<br>
but RULE-2:"not more than 15 messages in 60seconds" (limits rule 1 about 75%)"(request-rate))

#### Measurement No 4: - AUTH
- creating identities: keypairs = hash, padding, encryption: ECDSA-SALT-RSA
- Identities(created & existing) authorized, unauthorized, spammer/attacker, bot, multiple accounts, identity theft
- who is who (identity + request-rate + block/delete)

You can start to see some patterns coming up from playing with red,yellow and green, kind of a puzzle...

I will start by locking the whole system up and myself out, and then start open it a bit, look what happens, maybe unlock it a bit further... You'll get the point...

# Usecase dependant relay strategy - With and without identities (signatures)<br>

<br><br>

![image](https://user-images.githubusercontent.com/67427045/215083031-ce707d7e-876f-4c57-9508-0c9567cfeb31.png)<br>

<br><br>

# Incentivise API consumers with high bandwidth needs, and in turn grow the whole Gun Ecosystem
  
>  I know people want those features, but I'm trying to encourage people in the opposite direction: combine together into a large DHT so we all can reuse each other's spare bandwidth/compute. AMark

I made thoughts into your direction, and if this usecase dependant relay strategies (slide under this post) were integrated into Gun, that would be awesome!
Maybe an extension of AXE? 🔥🔥🔥

### How does it work?
After the first 24 hours of a relay relaying data to and from multiple dapps/applications from different sources, it generates a list (just for the sake of explaining, same with Google, Fortnite, Dtube) which gets updated every 24h or less.

This list would look basically like this:

24h USAGE:
- hans blog: 100mb
- peters travel blog: 500 mb
- toms videochat: 1000mb
- dtube: 10000mb
- fortnite: 100000mb
- google maps 1000000mb

### From here could the AXE extension do the following:

#### Group the request sources by bandwidth usage and sowith grant access to only relays to dapps/apps with similar bandwidth need.
- So hans and peter in group A( low-level dapps)
- tom and dtube in group B (intermediate-level dapps)
- fortnite and google maps in group C (intensive dapps)

#### Group C could be incentiviced to create more Gun relay infrastructure for the whole Gun Ecosystem (they have the money, so why not?)
- the AXE extension limits the bandwidth of the highest consumer to the median of all consumers. Same with the second highest bandwitdth consumer and so on. Continously till a level is reached, were the group C bandwidth consumers do not slow down group A and B consumers using the same relay.
(if efficiency < 75% => balance mediate)
- this means for intensive consumers (group C) like Google or Fortine, that they MUST invest in the infrastructure(more relays), to raise that median. else they cant run their service, because the bandwidth will not be enough.
- but at the same time, its an investion into the whole Gun Ecosystem. So ABC.

PS: more groups A,B,C,D... for a finer grain are possible of course, this is just for the sake of explaining

<br><br>

# EXPERIMENTAL THOUGHTS: This is a concept inspired by [OPEN ID](https://en.wikipedia.org/wiki/OpenID) (Work in Progress)

### Short reminder of what OPEN ID is
The end user interacts with a relying party (such as a website) that provides an option to specify an OpenID for the purposes of authentication; an end user typically has previously registered an OpenID (e.g. ```alice.openid.example.org```) with an OpenID provider (e.g. ```openid.example.org```)<br>
<br>

### What did it inspire me to?
The problem in decentralization that comes up often is that "browsers can't listen to ports" which is pretty much the decision of the browser industry. So we are all locked in somewhat.<br>
<br>
"browsers can't listen to ports" means that browsers can send data to an adress and request something. But your browser (actually the device: computer, smartphone etc.) does not have a fixed address (the address switches), so your browser can't listen to incoming requests. (can't listen to ports, because there are no ports)<br>
<br>
This is the reason why you need a station in the middle (server, relay, signaling server etc.) When you and your chatpartner use Whatsapp, you both are connected to https://whatsapp.com, which manages your message exchange. A connected to whatsapp.com, B connected to whatsapp.com, we have line, signal.<br>
<br>
Today we use domains to make our servers, relays, signaling server, website, webapp etc. available to the public.<br>
Make them listen to that domain's "ports"<br>
<br>
But what if every person had a domain? Like everybody today is having a telephone-number...<br>
What if every browser had a domain? (browser to browser calls, browser sync, browser cms, browser whatnotelse...)

### Example
1. My domain is: myname.com
2. myname.com connects to a Gun relay, which is a decentralized back-end, a graph database.
3. My notebook, pc, and smartphone have an app, browser-extension, or webapp(myname.com), which connects regularly to myname.com to check for new data.
4. If you like to talk call myname.com, or message me on myname.com, or send whatever to myname.com (my all three devices "telephone-number")
5. This way every browser can listen to outside traffic. Ad-hoc sharing gets sweet as cake i imagine.
