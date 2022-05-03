# file-sharer

I want to write an application in Rust or Golang that is used for fast, easy and secure p2p file-sharing.
A use-case would be for example if you want to send that 2GB folder of holiday-pictures to a friend.

For a first test I used nginx with a self-signed certificate and forced usage of TLS 1.2/1.3 with strong crypto primitives, but there are a few points I want to mention
1) [-] It is annoying, that the connection is displayed as not secure because of the self-signed certificate
2) [+] Downloading the file and all the crypto stuff is handled by the browser/nginx
3) [+] It's very fast and well optimized because nginx/chrome/firefox are very well opimized
4) [-] When downloading a folder, each file has to be downloaded manually. Storing a .zip containing all files for convenience would be a waste of space.
5) [-] Port-forwarding is needed on the server-side
6) [-] Filenames are embedded in the URL which might be unwanted
7) [-] I'd like to use Curve448 which is not supported by most webbrowsers

With a standalone application I think some problems could be solved rather easily
- 4) The app could handle downloading each file in a directory sequentially
- 5) NAT Hole-punching could remove the need for port-forwarding
- 6) Filenames could be put in a (encrypted) json-response
- 7) There are implementations for Curve448 operations in Rust/Golang

- Server configuration
  - A list of folders/files to share

How I imagine a interaction where B wants to download files from A
1) A starts the server 
    1) A new Private/Public key-pair is generated
    2) the fingerprint of the public key is printed to the console
2) A sends (ip:port, fingerprint) to B through some chat
3) B starts the client and enters ip:port received from A
4) B receives the public-key from the server and compares the fingerprint
6) A and B establish a shared secret with Diffie-Hellman that is used for AES
7) B receives a list of files available to download from A
8) B selects what to download from A
9) A streams the file(s) as AES-encrypted chunks to B where each chunk has a checksum for integrity (e.g. AEAD?)
