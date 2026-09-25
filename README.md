🚀 Zero-Rupee Termux Website Hosting

<p align="center"><img src="https://capsule-render.vercel.app/api?type=waving&color=0:111827,50:2563eb,100:06b6d4&height=220&section=header&text=Host%20Your%20Website%20From%20Android&fontSize=42&fontColor=ffffff&animation=fadeIn&fontAlignY=38&desc=Termux%20%7C%20IPv6%20%7C%20DuckDNS%20%7C%20Caddy%20%7C%20HTTPS&descAlignY=60&descSize=17" width="100%"/></p><p align="center"><a href="https://github.com/termux/termux-app">
<img src="https://img.shields.io/badge/Platform-Android-3DDC84?style=for-the-badge&logo=android&logoColor=white">
</a><a href="https://termux.dev/">
<img src="https://img.shields.io/badge/Termux-Supported-000000?style=for-the-badge&logo=gnu-bash&logoColor=white">
</a><a href="https://www.duckdns.org/">
<img src="https://img.shields.io/badge/DNS-DuckDNS-orange?style=for-the-badge">
</a><a href="https://caddyserver.com/">
<img src="https://img.shields.io/badge/HTTPS-Caddy-1f88c5?style=for-the-badge">
</a></p><p align="center"><b>Turn an Android phone + mobile data into a real Internet-accessible web server.</b>

<br>No VPS • No Wi-Fi router • No paid hosting • Free hostname • Automatic IPv6 updates • HTTPS

</p>---

✨ What Is This?

This project shows how to host a real website directly from an Android phone running Termux.

The phone is the server.

Your website files remain on the phone, while:

🌍 Internet
     │
     ▼
Free DuckDNS hostname
     │
     ▼
Airtel / Mobile IPv6
     │
     ▼
📱 Android
     │
     ▼
Termux
     │
     ├── DuckDNS updater
     │
     └── Caddy
           │
           ├── HTTPS
           └── Website files

The final result looks like:

https://YOURNAME.duckdns.org:8443

---

🎬 Demo

<p align="center"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=24&pause=1000&color=00D9FF&center=true&vCenter=true&width=800&lines=Android+%3D+Your+Web+Server;Termux+%2B+IPv6+%2B+DuckDNS;Caddy+%2B+Let's+Encrypt;Free+HTTPS+Website;One+Command+To+Go+Live+%F0%9F%9A%80" alt="Animated typing"></p>---

⚡ Features

Feature| Included
📱 Android hosting| ✅
🖥️ Termux| ✅
🌐 Mobile data| ✅
🌍 Public IPv6| ✅
🆓 Free hostname| ✅
🔄 Dynamic IPv6 updates| ✅
🔐 HTTPS| ✅
🧾 Let's Encrypt| ✅
🚀 One-command startup| ✅
🔋 Wake lock| ✅
🔄 Automatic startup after reboot| ✅
🎨 Premium website template| ✅
💰 VPS required| ❌
💳 Paid hosting required| ❌

---

🧠 How It Works

flowchart TD
    A["🌍 Internet Visitor"] --> B["🌐 DuckDNS"]
    B --> C["AAAA DNS Record"]
    C --> D["📡 Mobile IPv6"]
    D --> E["📱 Android"]
    E --> F["⚡ Termux"]
    F --> G["🚀 Caddy :8443"]
    G --> H["🔐 HTTPS"]
    G --> I["📁 Website Files"]

    F --> J["🔄 IPv6 Updater"]
    J --> B

    G --> K["Let's Encrypt"]
    K --> B

The important part

Your mobile network may give Android:

100.116.x.x

for IPv4.

That is carrier-grade NAT space.

But Android may simultaneously receive a globally routable IPv6 such as:

2401:....

That IPv6 can potentially receive incoming traffic if the carrier firewall permits it.

---

⚠️ Important: IPv6 Requirement

This project is designed around direct IPv6 hosting.

Check:

ip -6 addr

You need a global IPv6 address similar to:

inet6 2401:.... scope global

If you only have:

fe80::....

that is a link-local IPv6 address and isn't enough.

---

📱 1. Install Termux

Use a current Termux distribution.

Official resources:

- https://termux.dev/
- https://github.com/termux/termux-app

After installing:

pkg update
pkg upgrade

Install basic packages:

pkg install curl git python iproute2 dnsutils

---

🌐 2. Check Your IPv6

Run:

ip -6 addr

Or:

ip -6 route show default

You should see a global IPv6.

For example:

inet6 2401:4900:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx/64 scope global

---

🧪 3. Test Your Server First

Create a test website:

mkdir -p ~/testserver
cd ~/testserver

echo '<h1>Hello from Android!</h1>' > index.html

Start the server:

python -m http.server 8080 --bind ::

You should see:

Serving HTTP on :: port 8080

---

🔍 4. Test IPv6 Locally

Find your global IPv6:

ip -6 addr show rmnet0

If your mobile interface isn't "rmnet0", find the interface used by:

ip -6 route show default

Then test:

curl -6 "http://[YOUR_IPV6]:8080"

Expected:

<h1>Hello from Android!</h1>

---

🌍 5. Test External Access

Your phone reaching itself isn't enough.

You need to test from a different Internet connection or an independent IPv6 port checker.

Test:

YOUR_IPV6:8080

If the external test reports:

OPEN

then your Android is reachable from the Internet over IPv6.

---

🆓 6. Create Your Free DuckDNS Hostname

Go to:

https://www.duckdns.org/

Create a hostname such as:

myandroidserver.duckdns.org

You will also receive a DuckDNS token.

🔐 NEVER publish the token

Do NOT put it inside:

GitHub
README.md
Screenshots
Public scripts
Website files

Store it locally.

---

🔑 7. Store Your DuckDNS Token

Run:

mkdir -p ~/duckdns
nano ~/.duckdns_token

Paste your token.

Save:

CTRL+O
ENTER
CTRL+X

Protect it:

chmod 600 ~/.duckdns_token

Verify without displaying the token:

[ -r ~/.duckdns_token ] && echo "Token file OK"

---

🔄 8. Automatic IPv6 → DuckDNS Updater

Create:

nano ~/duckdns/update-ipv6.sh

Paste:

#!/data/data/com.termux/files/usr/bin/bash

DOMAIN="YOUR_DUCKDNS_SUBDOMAIN"
TOKEN_FILE="$HOME/.duckdns_token"

if [ ! -r "$TOKEN_FILE" ]; then
    echo "ERROR: $TOKEN_FILE not found."
    exit 1
fi

TOKEN="$(cat "$TOKEN_FILE")"

IPV6="$(
    ip -6 route get 2606:4700:4700::1111 2>/dev/null |
    awk '{
        for (i=1; i<=NF; i++)
            if ($i == "src") {
                print $(i+1)
                exit
            }
    }'
)"

if [ -z "$IPV6" ]; then
    echo "ERROR: No usable global IPv6 found."
    exit 1
fi

RESULT="$(
    curl -fsS \
      --get \
      --data-urlencode "domains=$DOMAIN" \
      --data-urlencode "token=$TOKEN" \
      --data-urlencode "ipv6=$IPV6" \
      "https://www.duckdns.org/update"
)"

if [ "$RESULT" = "OK" ]; then
    echo "DuckDNS updated:"
    echo "$DOMAIN -> $IPV6"
else
    echo "DuckDNS update failed:"
    echo "$RESULT"
    exit 1
fi

Change:

DOMAIN="YOUR_DUCKDNS_SUBDOMAIN"

For example:

DOMAIN="myandroidserver"

Then:

chmod +x ~/duckdns/update-ipv6.sh

Run:

~/duckdns/update-ipv6.sh

Expected:

DuckDNS updated:
myandroidserver -> 2401:....

---

🔎 9. Verify DNS

Install DNS tools if necessary:

pkg install dnsutils

Then:

dig AAAA YOUR_DUCKDNS_SUBDOMAIN.duckdns.org

You should see your IPv6 in the answer.

---

🚀 10. Install Caddy

pkg update
pkg install golang git clang

Install xcaddy:

go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest

Add Go binaries to PATH:

export PATH="$PATH:$(go env GOPATH)/bin"

Enable CGO:

export CGO_ENABLED=1

Verify:

go version
xcaddy version
clang --version

---

🧩 11. Build Caddy With DuckDNS Support

Go to your website directory:

cd ~/testserver/YOUR_WEBSITE_DIRECTORY

Build:

xcaddy build --with github.com/caddy-dns/duckdns

This creates:

./caddy

Make sure it exists:

ls -lh ./caddy

Test:

./caddy version

Now check for the DuckDNS module:

./caddy list-modules | grep dns.providers.duckdns

Expected:

dns.providers.duckdns

If nothing appears, do not continue. The custom Caddy binary does not contain the DuckDNS provider.

---

🔐 12. Why We Use DNS-01

A common Caddy setup tries to use HTTP-01.

That requires:

TCP 80

or TLS-ALPN-01 requires:

TCP 443

Unrooted Android applications normally cannot bind to these privileged ports.

Therefore we use:

DNS-01

The certificate authority verifies domain ownership through DNS instead.

sequenceDiagram
    participant C as Caddy
    participant D as DuckDNS
    participant L as Let's Encrypt

    C->>L: Request certificate
    L->>C: DNS challenge
    C->>D: Create TXT record
    L->>D: Check TXT record
    D-->>L: TXT record found
    L-->>C: Certificate
    C->>C: Enable HTTPS

---

📝 13. Create Caddyfile

Create:

mkdir -p ~/caddy
nano ~/caddy/Caddyfile

Use:

{
    https_port 8443
    auto_https disable_redirects
}

YOUR_DUCKDNS_SUBDOMAIN.duckdns.org:8443 {
    tls {
        dns duckdns {env.DUCKDNS_API_TOKEN}
    }

    root * /data/data/com.termux/files/home/testserver/YOUR_WEBSITE_DIRECTORY

    file_server
}

Replace:

YOUR_DUCKDNS_SUBDOMAIN

and:

YOUR_WEBSITE_DIRECTORY

with your actual values.

Format:

./caddy fmt --overwrite ~/caddy/Caddyfile

Validate:

./caddy validate --config ~/caddy/Caddyfile

Expected:

Valid configuration

---

🔑 14. Give Caddy Its DuckDNS Token

Do not put the token directly inside your Caddyfile.

Instead:

export DUCKDNS_API_TOKEN="$(cat ~/.duckdns_token)"

Verify safely:

[ -n "$DUCKDNS_API_TOKEN" ] && echo "DuckDNS token loaded"

---

🔐 15. Start HTTPS

Run:

./caddy run --config ~/caddy/Caddyfile

Caddy should obtain the certificate using DNS-01.

Your website should become:

https://YOUR_DUCKDNS_SUBDOMAIN.duckdns.org:8443

Test:

curl -6 https://YOUR_DUCKDNS_SUBDOMAIN.duckdns.org:8443

---

⚡ 16. One-Command Startup

Once everything works, create:

nano ~/start-site

Paste:

#!/data/data/com.termux/files/usr/bin/bash

set -u

DOMAIN="YOUR_DUCKDNS_SUBDOMAIN"

TOKEN_FILE="$HOME/.duckdns_token"

CADDY="$HOME/testserver/YOUR_WEBSITE_DIRECTORY/caddy"

CADDYFILE="$HOME/caddy/Caddyfile"


if [ ! -r "$TOKEN_FILE" ]; then
    echo "ERROR: $TOKEN_FILE is missing."
    exit 1
fi


if [ ! -x "$CADDY" ]; then
    echo "ERROR: Caddy binary not found:"
    echo "$CADDY"
    exit 1
fi


TOKEN="$(cat "$TOKEN_FILE")"


update_dns() {

    IPV6="$(
        ip -6 route get 2606:4700:4700::1111 2>/dev/null |
        awk '{
            for (i=1; i<=NF; i++)
                if ($i == "src") {
                    print $(i+1)
                    exit
                }
        }'
    )'


    if [ -z "$IPV6" ]; then
        echo "[$(date)] No usable global IPv6."
        return 1
    fi


    RESULT="$(
        curl -fsS \
          --get \
          --data-urlencode "domains=$DOMAIN" \
          --data-urlencode "token=$TOKEN" \
          --data-urlencode "ipv6=$IPV6" \
          "https://www.duckdns.org/update"
    )"


    if [ "$RESULT" = "OK" ]; then
        echo "[$(date)] DuckDNS OK: $IPV6"
    else
        echo "[$(date)] DuckDNS FAILED: $RESULT"
    fi
}


cleanup() {

    if [ -n "${DNS_PID:-}" ]; then
        kill "$DNS_PID" 2>/dev/null || true
    fi

    if command -v termux-wake-unlock >/dev/null 2>&1; then
        termux-wake-unlock
    fi
}


trap cleanup EXIT INT TERM


echo "======================================"
echo "       ANDROID WEBSITE SERVER"
echo "======================================"


if command -v termux-wake-lock >/dev/null 2>&1; then
    termux-wake-lock
fi


update_dns


export DUCKDNS_API_TOKEN="$TOKEN"


echo
echo "Checking Caddy..."


"$CADDY" validate \
    --config "$CADDYFILE" || exit 1


(
    while true; do

        sleep 300

        update_dns

    done
) &


DNS_PID=$!


echo
echo "======================================"
echo "          SITE IS LIVE 🚀"
echo "======================================"
echo
echo "https://$DOMAIN.duckdns.org:8443"
echo


exec "$CADDY" run \
    --config "$CADDYFILE"

Make executable:

chmod +x ~/start-site

Now the entire website can be launched with:

~/start-site

---

🎯 The Goal

After the initial setup:

ONE COMMAND
     ↓
~/start-site
     ↓
Detect IPv6
     ↓
Update DuckDNS
     ↓
Load token
     ↓
Validate Caddy
     ↓
Start DNS updater
     ↓
Start Caddy
     ↓
HTTPS
     ↓
🌍 WEBSITE LIVE

---

🔋 17. Keep Android Awake

If available:

termux-wake-lock

Release it:

termux-wake-unlock

The launcher automatically uses the wake lock while the server is running.

---

🔄 18. Start Automatically After Reboot

Install Termux:Boot.

Official project:

https://github.com/termux/termux-boot

Open the Termux:Boot app once after installation.

Then:

mkdir -p ~/.termux/boot

Create:

nano ~/.termux/boot/start-site

Paste:

#!/data/data/com.termux/files/usr/bin/bash

termux-wake-lock

sleep 10

for i in $(seq 1 30); do

    if ip -6 route show default | grep -q '^default'; then
        break
    fi

    sleep 2

done

exec "$HOME/start-site"

Make executable:

chmod +x ~/.termux/boot/start-site

Now:

📱 Phone reboot
       ↓
Termux:Boot
       ↓
Wait for mobile network
       ↓
Detect IPv6
       ↓
Update DuckDNS
       ↓
Start Caddy
       ↓
🔐 HTTPS
       ↓
🌍 Website online

---

🎨 19. Premium Website Example

Create:

mkdir -p ~/testserver/premium-site
cd ~/testserver/premium-site

---

"index.html"

nano index.html

Paste:

<!doctype html>
<html lang="en">

<head>

<meta charset="utf-8">

<meta name="viewport"
      content="width=device-width,initial-scale=1">

<meta name="description"
      content="Premium self-hosted website">

<title>NOVA — Premium Web Experience</title>

<link rel="stylesheet"
      href="style.css">

</head>


<body>

<div class="noise"></div>


<header class="nav">

<a class="brand" href="#">
NOVA<span>.</span>
</a>


<nav>

<a href="#features">
Features
</a>

<a href="#about">
About
</a>

<a class="nav-btn" href="#contact">
Launch
</a>

</nav>

</header>


<main>


<section class="hero">

<div class="eyebrow">
SELF-HOSTED • FAST • PRIVATE
</div>


<h1>
Your website.
<br>
<em>Your server.</em>
</h1>


<p class="hero-copy">

A polished starter website designed
to run beautifully from an Android
server powered by Termux.

</p>


<div class="actions">

<a class="btn primary"
   href="#features">
Explore
</a>


<a class="btn ghost"
   href="#about">
Learn more
</a>

</div>


<div class="orb orb-a"></div>

<div class="orb orb-b"></div>

</section>



<section id="features"
         class="section">


<div class="section-head">

<span>
01
</span>

<h2>
Built for the web.
</h2>

</div>


<div class="grid">


<article class="card">

<span class="icon">
✦
</span>

<h3>
Responsive
</h3>

<p>
Designed for phones first,
then scaled smoothly to desktop.
</p>

</article>



<article class="card">

<span class="icon">
↗
</span>

<h3>
Lightweight
</h3>

<p>
Plain HTML, CSS and JavaScript.
No framework required.
</p>

</article>



<article class="card">

<span class="icon">
∞
</span>

<h3>
Self-hosted
</h3>

<p>
Your files live on your own device
and are served by Caddy.
</p>

</article>


</div>

</section>



<section id="about"
         class="split">


<div>

<span class="eyebrow">
02 / THE IDEA
</span>

<h2>
Small hardware.
<br>
Big presence.
</h2>

</div>


<p>

Replace this text with your story,
portfolio, shop, business,
documentation or project.

This template has no external
framework dependencies.

</p>

</section>



<section id="contact"
         class="cta">


<p class="eyebrow">
03 / READY
</p>


<h2>
Make the Internet
<br>
your canvas.
</h2>


<a class="btn primary"
   href="mailto:hello@example.com">

Contact

</a>


</section>

</main>



<footer>

<span>
NOVA.
</span>

<span>
Hosted from Termux
</span>

</footer>


<script src="script.js"></script>

</body>

</html>

---

🎨 "style.css"

nano style.css

Paste:

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}


:root {

    --bg: #07070a;

    --text: #f4f4f5;

    --muted: #a1a1aa;

    --line: #24242b;

    --accent: #b8ff6a;

}


html {

    scroll-behavior: smooth;

}


body {

    font-family:
        Inter,
        ui-sans-serif,
        system-ui,
        -apple-system,
        BlinkMacSystemFont,
        "Segoe UI",
        sans-serif;

    background: var(--bg);

    color: var(--text);

    line-height: 1.6;

    overflow-x: hidden;

}


a {

    color: inherit;

    text-decoration: none;

}


.noise {

    position: fixed;

    inset: 0;

    pointer-events: none;

    opacity: .035;

}


.nav {

    height: 76px;

    display: flex;

    align-items: center;

    justify-content: space-between;

    padding: 0 6vw;

    border-bottom: 1px solid var(--line);

    position: relative;

    z-index: 5;

    background: #07070acc;

    backdrop-filter: blur(14px);

}


.brand {

    font-weight: 900;

    letter-spacing: .16em;

}


.brand span {

    color: var(--accent);

}


nav {

    display: flex;

    align-items: center;

    gap: 28px;

    font-size: .9rem;

    color: var(--muted);

}


nav a:hover {

    color: var(--text);

}


.nav-btn {

    border: 1px solid var(--line);

    padding: 9px 16px;

    border-radius: 999px;

}


.hero {

    min-height:
        calc(100vh - 76px);

    display: flex;

    flex-direction: column;

    justify-content: center;

    padding: 10vh 9vw;

    position: relative;

    isolation: isolate;

}


.eyebrow {

    font-size: .72rem;

    letter-spacing: .22em;

    color: var(--muted);

    font-weight: 800;

}


.hero h1 {

    font-size:
        clamp(4rem, 12vw, 9rem);

    line-height: .86;

    letter-spacing: -.065em;

    max-width: 1000px;

    margin: 24px 0;

}


.hero h1 em {

    font-style: normal;

    color: var(--accent);

}


.hero-copy {

    max-width: 580px;

    color: var(--muted);

    font-size:
        clamp(1rem, 2vw, 1.25rem);

}


.actions {

    display: flex;

    gap: 12px;

    margin-top: 32px;

}


.btn {

    display: inline-flex;

    padding: 13px 20px;

    border-radius: 999px;

    font-weight: 800;

    border: 1px solid var(--line);

}


.primary {

    background: var(--accent);

    color: #0a0a0a;

    border-color: var(--accent);

}


.ghost {

    color: var(--text);

}


.orb {

    position: absolute;

    border-radius: 50%;

    filter: blur(3px);

    z-index: -1;

}


.orb-a {

    width: 380px;

    height: 380px;

    right: 4vw;

    top: 15%;

    background:
        radial-gradient(
            circle,
            #b8ff6a44,
            transparent 65%
        );

}


.orb-b {

    width: 500px;

    height: 500px;

    left: -15%;

    bottom: -25%;

    background:
        radial-gradient(
            circle,
            #8b5cf633,
            transparent 65%
        );

}


.section,
.split,
.cta {

    padding: 110px 9vw;

    border-top: 1px solid var(--line);

}


.section-head {

    display: flex;

    gap: 30px;

    align-items: baseline;

    margin-bottom: 40px;

}


.section-head span {

    color: var(--accent);

    font-family: monospace;

}


.section h2,
.split h2,
.cta h2 {

    font-size:
        clamp(2.5rem, 6vw, 5.5rem);

    line-height: .95;

    letter-spacing: -.05em;

}


.grid {

    display: grid;

    grid-template-columns:
        repeat(3, 1fr);

    gap: 14px;

}


.card {

    border: 1px solid var(--line);

    padding: 30px;

    min-height: 220px;

    border-radius: 24px;

    background: #0b0b0f;

    transition: .3s;

}


.card:hover {

    transform:
        translateY(-6px);

    border-color: #4a4a54;

}


.icon {

    font-size: 1.5rem;

    color: var(--accent);

}


.card h3 {

    margin:
        40px 0 8px;

}


.card p,
.split > p {

    color: var(--muted);

}


.split {

    display: grid;

    grid-template-columns:
        1fr 1fr;

    gap: 8vw;

    align-items: end;

}


.split > p {

    font-size: 1.15rem;

    max-width: 560px;

}


.cta {

    text-align: center;

}


.cta h2 {

    margin:
        25px auto 35px;

}


footer {

    display: flex;

    justify-content: space-between;

    padding: 30px 9vw;

    color: var(--muted);

    font-size: .8rem;

    border-top: 1px solid var(--line);

}


@media (max-width: 760px) {

    nav a:not(.nav-btn) {

        display: none;

    }


    .hero {

        padding-left: 7vw;

        padding-right: 7vw;

    }


    .grid,
    .split {

        grid-template-columns: 1fr;

    }


    .section,
    .split,
    .cta {

        padding:
            80px 7vw;

    }


    .hero h1 {

        font-size:
            clamp(3.5rem, 18vw, 6rem);

    }

}

---

✨ "script.js"

nano script.js

Paste:

const reveal = new IntersectionObserver(
    (entries) => {

        entries.forEach((entry) => {

            if (!entry.isIntersecting) {
                return;
            }

            entry.target.animate(

                [
                    {
                        opacity: 0,
                        transform: "translateY(22px)"
                    },

                    {
                        opacity: 1,
                        transform: "translateY(0)"
                    }
                ],

                {
                    duration: 650,
                    easing:
                        "cubic-bezier(.2,.7,.2,1)",
                    fill: "forwards"
                }

            );

            reveal.unobserve(entry.target);

        });

    },

    {
        threshold: 0.12
    }
);


document
    .querySelectorAll(
        ".card,.split,.cta"
    )
    .forEach(
        (element) =>
            reveal.observe(element)
    );

---

📂 Website Structure

premium-site/
│
├── index.html
├── style.css
└── script.js

---

🛠️ Troubleshooting

❌ "permission denied" on port 443

Example:

listen tcp :443: bind: permission denied

This is expected on an unrooted Android environment.

Use:

8443

instead.

---

❌ "permission denied" on port 80

Example:

listen tcp :80: bind: permission denied

Use DNS-01 instead of HTTP-01.

Caddy configuration:

{
    https_port 8443
    auto_https disable_redirects
}

And:

tls {
    dns duckdns {env.DUCKDNS_API_TOKEN}
}

---

❌ DuckDNS module missing

Run:

./caddy list-modules | grep dns.providers.duckdns

If there is no output:

export CGO_ENABLED=1

xcaddy build \
    --with github.com/caddy-dns/duckdns

---

❌ CGO error

If you see:

android/arm requires external (cgo) linking,
but cgo is not enabled

Run:

pkg install clang

export CGO_ENABLED=1

Then rebuild:

xcaddy build \
    --with github.com/caddy-dns/duckdns

---

❌ "getent: command not found"

Termux may not include "getent".

Use:

pkg install dnsutils

Then:

dig AAAA YOURNAME.duckdns.org

---

❌ DuckDNS says "KO"

Check:

[ -r ~/.duckdns_token ] && echo "Token exists"

Then:

~/duckdns/update-ipv6.sh

Never paste your token into a public issue.

---

❌ No global IPv6

Run:

ip -6 addr

Then:

ip -6 route show default

Toggle mobile data or airplane mode and retry.

---

❌ Website works locally but not externally

Test:

curl -6 "http://[YOUR_IPV6]:8080"

Then test from an independent IPv6 connection.

Your carrier must permit inbound IPv6 traffic.

---

❌ Website works for some people but not others

This usually indicates an IPv6 reachability issue.

A visitor without IPv6 cannot directly connect to an IPv6-only server.

For broad IPv4 + IPv6 compatibility, use a tunnel, reverse proxy, or another dual-stack frontend.

---

🔐 Security

NEVER expose this

~/.duckdns_token

Never publish:

DuckDNS tokens
Private keys
Caddy storage
Passwords
API keys
Database credentials
Personal documents

Your website root should contain only files intended to be public.

---

🚫 Don't Do This

❌ Put your DuckDNS token in "index.html"

❌ Commit your token to GitHub

❌ Run:

chmod 777 ~

❌ Expose your entire home directory

❌ Run unnecessary network services

❌ Disable Android security features

❌ Assume IPv6 means everyone on IPv4 can access your site

---

📁 Recommended Structure

$HOME/
│
├── .duckdns_token
│
├── start-site
│
├── duckdns/
│   └── update-ipv6.sh
│
├── caddy/
│   └── Caddyfile
│
├── .termux/
│   └── boot/
│       └── start-site
│
└── testserver/
    └── YOUR_WEBSITE/
        │
        ├── caddy
        ├── index.html
        ├── style.css
        └── script.js

---

🚀 Final One-Command Setup

After everything has been configured:

~/start-site

That's it.

The launcher:

╔══════════════════════════════╗
║       START-SITE 🚀          ║
╚══════════════════════════════╝
              │
              ▼
       Detect IPv6
              │
              ▼
       Update DuckDNS
              │
              ▼
       Load credentials
              │
              ▼
       Validate Caddy
              │
              ▼
       Start DNS updater
              │
              ▼
       Start Caddy
              │
              ▼
       🔐 HTTPS
              │
              ▼
       🌍 WEBSITE LIVE

---

🧪 Useful Commands

Check IPv6

ip -6 addr

Check default IPv6 route

ip -6 route show default

Check DuckDNS

dig AAAA YOURNAME.duckdns.org

Update DNS manually

~/duckdns/update-ipv6.sh

Validate Caddy

./caddy validate \
    --config ~/caddy/Caddyfile

Check DuckDNS module

./caddy list-modules |
grep dns.providers.duckdns

Start server

~/start-site

Stop server

CTRL+C

Keep Android awake

termux-wake-lock

Release wake lock

termux-wake-unlock

---

🌍 What You're Actually Building

This is not GitHub Pages.

This is not conventional shared hosting.

This is your:

📱 Android
+
📡 Mobile Internet
+
🌐 IPv6
+
🆓 DuckDNS
+
🔐 Caddy
+
📁 Your website

acting as a web server.

---

⚠️ Production Reality

A phone can absolutely serve a website, but it isn't equivalent to a professional datacenter server.

Possible causes of downtime include:

- Android reboot
- Battery optimization
- Phone overheating
- Mobile signal loss
- Mobile-data disconnect
- IPv6 address changes
- Carrier maintenance
- Carrier firewall changes
- IPv6-only client limitations

For a personal project, portfolio, experiment, documentation site or learning project, this architecture can be extremely useful.

For mission-critical production workloads, use appropriate redundant infrastructure.

---

📚 Documentation

Termux

https://termux.dev/

Termux:Boot

https://github.com/termux/termux-boot

DuckDNS

https://www.duckdns.org/

Caddy

https://caddyserver.com/

Caddy Automatic HTTPS

https://caddyserver.com/docs/automatic-https

Caddy Build

https://caddyserver.com/docs/build

DuckDNS Caddy Plugin

https://github.com/caddy-dns/duckdns

---

❤️ Credits

Built as an experiment to demonstrate that an Android phone can become a real Internet-accessible web server using free software and a mobile IPv6 connection.

Android
   +
Termux
   +
IPv6
   +
DuckDNS
   +
Caddy
   =
🌍 Your Own Web Server

---

<p align="center"><img src="https://capsule-render.vercel.app/api?type=waving&color=0:06b6d4,50:2563eb,100:111827&height=140&section=footer&animation=fadeIn" width="100%"/></p><p align="center"><b>🚀 Build it. Host it. Own the server.</b>

</p>
