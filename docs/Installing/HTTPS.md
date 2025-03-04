---
title: Activating HTTPS
---

You’ll need to enable HTTPS on your home server in order to safely use all of Actual’s features. **You don’t need to follow these steps** if you run the server on your own computer and only access it through `localhost`, or if you’re using a cloud provider that handles HTTPS for you. There are a few different ways to get HTTPS to work, depending on what you’d prefer to do.

1. Use a self-signed certificate. This is the easiest way to get HTTPS working, but it will cause your browser to display a warning that the certificate is invalid. Additionally, if anyone gets access to this certificate, they can intercept most secure traffic on your computer.
   - A command line tool like [mkcert](https://github.com/FiloSottile/mkcert) can automate this process.
   - Alternately, you can manually generate the certificates. Install OpenSSL for your operating system, then run `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout selfhost.key -out selfhost.crt` in a terminal to generate the certificate and private key. You’ll need to enter a two-letter country code to get the `.crt` file to be generated, but you can leave the rest of the fields blank (just hit enter at each prompt). Move the `selfhost.key` and `selfhost.crt` files a location accessible to the Actual server.
2. Connect your server to a domain you control and make it public to the Internet. You could use a tool like [certbot](https://certbot.eff.org) to generate a valid certificate once you have the domain set up.
3. Use a service like [Tailscale](https://tailscale.com/kb/1153/enabling-https/) or [Caddy](https://caddyserver.com/docs/automatic-https#dns-challenge) that allows you to create a valid HTTPS certificate without having to expose your server to the wider internet.

Once you have the certificate, you’ll need to configure Actual to use it. There are two ways to do this:

1. **Configuring with `config.json`**: Create a `config.json` file in the same folder where you run Actual (or `/data` if you’re using a Docker container). Put the paths to the `.key` and `.crt` files in the file. Note: if you’re using Docker or a similar container environment, make sure the paths are accessible to the container. For example:
   ```json
   {
     "https": {
       "keyPath": "/data/selfhost.key",
       "certPath": "/data/selfhost.crt"
     }
   }
   ```
2. **Configuring with environment variables**: If you can’t easily create new files, you can also configure HTTPS using environment variables. Set the `ACTUAL_HTTPS_KEY` and `ACTUAL_HTTPS_CERT` environment variables to the contents of the `.key` and `.crt` files, respectively. If you’re unable to include newlines in the environment variable values, you can replace any newlines with `\n` and Actual will automatically convert them back to newlines.
