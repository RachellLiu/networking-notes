# Mutual TLS (mTLS)

mTLS ensures that the parties at each end of a network connection are who they claim to be by verifying that they both have the correct private key. The information within their respective TLS certificates provides additional verification.

mTLS is often used in a Zero Trust security framework to verify users, devices, and servers within an organization. It can also help keep APIs secure.(Zero Trust means that no user, device, or network traffic is trusted by default, an approach that helps eliminate many security vulnerabilities.)

mTLS is more often used in business-to-business (B2B) applications, where a limited number of programmatic and homogeneous clients are connecting to specific web services, the operational burden is limited, and security requirements are usually much higher as compared to consumer environments.

mTLS is used in a variety of applications, including: Web Services security, Databases security, API security, Microservices architecture, Service Mesh, IoT Security, Zero Trust Security.

![TLS](https://www.cloudflare.com/resources/images/slt3lc6tev37/37w1tzGsD4XvYUkQCHbWG8/6fbbb48d0f5077cc2c662a4cc6817b1c/how_tls_works-what_is_mutual_tls.png)

Normally in TLS, the server has a TLS certificate and a public/private key pair, while the client does not. The typical TLS process works like this:

1. Client connects to server
2. Server presents its TLS certificate
3. Client verifies the server's certificate
4. Client and server exchange information over encrypted TLS connection

![mTLS](https://www.cloudflare.com/resources/images/slt3lc6tev37/5SjaQfZzDLEGqyzFkA0AA4/d227a26bbd7bc6d24363e9b9aaabef55/how_mtls_works-what_is_mutual_tls.png)

In mTLS, however, both the client and server have a certificate, and both sides authenticate using their public/private key pair. Compared to regular TLS, there are additional steps in mTLS to verify both parties:

1. Client connects to server
2. Server presents its TLS certificate
3. Client verifies the server's certificate
4. **Client presents its TLS certificate**
5. **Server verifies the client's certificate**
6. **Server grants access**
7. Client and server exchange information over encrypted TLS connection

## Certificate management

Clients and servers merrily authenticate each other and then security happens. In practice, the massive practical challenge standing in the way of making mTLS work is certificate management.

Authentication in TLS works through public key cryptography and public key infrastructure. TLS authentication is based on something with the name of the **X.509 certificate**.

A X.509 certificate contains, among other things, an identity and public key. The public key also has a corresponding private key, which is not part of the certificate. The first half of authenticating yourself in TLS is showing your certificate to the other side and then using the private key to prove that the identity inside the certificate is yours. (The magic of public key cryptography is that anyone who copies the certificate can’t do this proof because they don’t have the private key. So you can be very free with the certificate, including sending it across plaintext channels or storing it in the open.)

![cert](https://assets-global.website-files.com/625ee9b2f6a4ec3997f9c11b/63f4ff5b95fafff49cfc4975_certificates.webp)

X.509 certificates are signed by a certificate authority (CA) denoting that the CA “trusts” the identity in that certificate. This is used for the second half of TLS authentication: if someone shows you their identity and proves they own it, you now have to decide whether you trust that identity. TLS uses a simple rule here: if the certificate is signed by a CA, and you trust that CA, you also trust the identity. How do you verify the CA’s signature of a certificate? By using the X.509 certificate of the CA itself.

The CA also issues certificates. To get a certificate, you first create the public key and private key pair. You keep the private key, private—we’ll never send it over the network—and you send the CA a certificate signing request (CSR) that contains the public key and your identity. If the CA approves the request, it creates the certificate, signs it, and sends it back to you.

Certificate management, then, is the challenge of creating and distributing all these certificates. We need to make sure there is a CA, that every service has its certificate, that every service can send it a CSR, and that the CA can send the certificates back to the service. We also need to make sure our CA is secure, and that no one ever has access to the private keys of each service, and that every service knows its own identity in a way that can’t be altered.

This certificate distribution challenge is compounded by the fact that in environments like Kubernetes, a “service” is in fact an ever-changed set of replicas that can be created or destroyed on the fly, each of which needs its own set of certificates.

And that is further compounded by the fact that, in practice, the best way anyone has found to mitigate certificate loss (i.e. what happens when someone unauthorized gets access to a secret key) is by certificate rotation: you give certificates very short lifetimes and re-issue them before they expire. This means we need to repeat the whole CSR and certificate flow, for each replica, every hours.

And even that is further compounded by the fact that if we want to extend our secure communication across clusters, we need a way to ensure that the identities generated in one cluster can be consumed by the other cluster, but that if a whole cluster gets compromised, we can disable it without disabling every other cluster. We do this with more certificates.

In summary, doing mTLS involves a whole lot of certificates, all the time. The complexity of this challenge can be daunting. But despite that, mTLS has seen something of a renaissance in the world of Kubernetes. This is because Kubernetes unlocks a particular type of technology that makes mTLS feasible: [the service mesh](https://buoyant.io/service-mesh-manifesto).

## Why use mTLS?

mTLS helps ensure that traffic is secure and trusted in both directions between a client and server. This provides an additional layer of security for users who log in to an organization's network or applications. It also verifies connections with client devices that do not follow a login process, such as Internet of Things (IoT) devices.

mTLS prevents various kinds of attacks, including:

- **On-path attacks**: On-path attackers place themselves between a client and a server and intercept or modify communications between the two. When mTLS is used, on-path attackers cannot authenticate to either the client or the server, making this attack almost impossible to carry out.
- **Spoofing attacks**: Attackers can attempt to "spoof" (imitate) a web server to a user, or vice versa. Spoofing attacks are far more difficult when both sides have to authenticate with TLS certificates.
- **Credential stuffing**: Attackers use leaked sets of credentials from a data breach to try to log in as a legitimate user. Without a legitimately issued TLS certificate, credential stuffing attacks cannot be successful against organizations that use mTLS.
- **Brute force attacks**: Typically carried out with bots, a brute force attack is when an attacker uses rapid trial and error to guess a user's password. mTLS ensures that a password is not enough to gain access to an organization's network. (Rate limiting is another way to deal with this type of bot attack.)
- **Phishing attacks**: The goal of a phishing attack is often to steal user credentials, then use those credentials to compromise a network or an application. Even if a user falls for such an attack, the attacker still needs a TLS certificate and a corresponding private key in order to use those credentials.
- **Malicious API requests**: When used for API security, mTLS ensures that API requests come from legitimate, authenticated users only. This stops attackers from sending malicious API requests that aim to exploit a vulnerability or subvert the way the API is supposed to function.
