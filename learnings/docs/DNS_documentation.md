# Domain Name System (DNS)

## Table of Contents
1. [Introduction](#introduction)
2. [Why DNS Exists](#why-dns-exists)
3. [How DNS Works](#how-dns-works)
4. [DNS Hierarchy](#dns-hierarchy)
5. [Step-by-Step DNS Resolution Process](#step-by-step-dns-resolution-process)
6. [Caching Mechanism](#caching-mechanism)

---

## Introduction

**DNS (Domain Name System)** is a fundamental networking protocol that acts as the internet's phone book. It translates human-readable domain names (like `google.com`) into machine-readable IP addresses (like `142.250.185.46`).

### Key Concept
- **Computers** communicate using numbers (IP addresses)
- **Humans** remember names better than numbers
- **DNS** bridges this gap by resolving domain names to IP addresses

---

## Why DNS Exists

### The Problem
```
Humans prefer: google.com, amazon.com, yahoo.com
Computers need: 142.250.185.46, 205.251.242.103, 98.137.246.8
```

While you *could* type an IP address directly into your browser to access a website, this is impractical because:
- There are millions of websites on the internet
- IP addresses are difficult to memorize
- IP addresses can change over time

### The Solution
DNS allows users to type memorable domain names while computers continue to use IP addresses for communication.

---

## How DNS Works

DNS operates like a **phone book**:
1. You look up a **name** (domain)
2. DNS returns the **number** (IP address)
3. Your computer uses that number to connect

### Simple Example

```
┌─────────────────────────────────────────────────────────┐
│  User types: google.com                                 │
│             ↓                                           │
│  DNS translates: google.com → 142.250.185.46            │
│             ↓                                           │
│  Browser connects to: 142.250.185.46                    │
│             ↓                                           │
│  google webpage loads                                   │
└─────────────────────────────────────────────────────────┘
```

---

## DNS Hierarchy

DNS uses a hierarchical system with multiple levels of servers:

```
                    ┌─────────────────┐
                    │   Root Server   │
                    │   (13 sets)     │
                    └────────┬────────┘
                             │
                    ┌────────┴────────┐
                    │   TLD Server    │
                    │ (.com .net .org)│
                    └────────┬────────┘
                             │
                    ┌────────┴────────────┐
                    │  Authoritative NS   │
                    │  (Final Authority)  │
                    └─────────────────────┘
```

### DNS Server Types

| Server Type | Role | Example |
|------------|------|---------|
| **Resolver** | Your ISP's DNS server; first point of contact | Comcast DNS, Google DNS (8.8.8.8) |
| **Root Server** | Top of DNS hierarchy; directs to TLD servers | 13 sets worldwide |
| **TLD Server** | Manages top-level domains (.com, .net, .org) | .com TLD server |
| **Authoritative Name Server** | Knows everything about the domain; final authority | Google's name server |

---

## Step-by-Step DNS Resolution Process

### Visual Flow Diagram

```
┌──────────────┐
│   1. USER    │ Types: google.com
│   Computer   │
└──────┬───────┘
       │ (Query: What is IP of google.com?)
       ↓
┌──────────────────────┐
│  2. RESOLVER SERVER  │ (ISP - Internet Service Provider)
│    (Your ISP)        │ Checks cache → Not found
└──────┬───────────────┘
       │ (Query forwarded)
       ↓
┌──────────────────────┐
│   3. ROOT SERVER     │ (1 of 13 sets)
│   (Top Level)        │ "I don't know the IP, but ask .com TLD"
└──────┬───────────────┘
       │ (Redirects to TLD)
       ↓
┌──────────────────────┐
│   4. TLD SERVER      │ (.com domain)
│     (.com)           │ "I don't know the IP, but ask google's NS"
└──────┬───────────────┘
       │ (Redirects to Authoritative NS)
       ↓
┌──────────────────────┐
│ 5. AUTHORITATIVE NS  │ (google's Name Server)
│  (google's Server)   │ "The IP is 142.250.185.46"
└──────┬───────────────┘
       │ (Returns IP address)
       ↓
┌──────────────────────┐
│  6. RESOLVER SERVER  │ 
│   (Caches result)    │ Stores: google.com = 142.250.185.46
└──────┬───────────────┘
       │ (Returns IP to user)
       ↓
┌──────────────────────┐
│   7. USER COMPUTER   │
│  Connects to google  │ Browser loads google.com
└──────────────────────┘
```

![alt text](image-1.png)

### Detailed Steps

#### **Step 1: Initial Query**
- User types `google.com` in the web browser
- Browser checks its own cache memory
- Operating system checks its cache
- If not found, query sent to Resolver

#### **Step 2: Resolver Server (ISP)**
- Resolver receives the query from your computer
- Checks its own cache for `google.com`
- If not found, forwards query to Root Server

#### **Step 3: Root Server**
- Receives query for `google.com`
- Doesn't know the specific IP address
- Directs Resolver to the appropriate TLD server (.com)
- **Note:** 13 sets of root servers strategically placed worldwide, operated by 12 different organizations

#### **Step 4: TLD Server**
- Receives query for `google.com`
- Manages all .com domains
- Doesn't know the specific IP address
- Directs Resolver to google's Authoritative Name Server

#### **Step 5: Authoritative Name Server**
- **Final authority** for the domain
- Knows everything about `google.com` including its IP address
- Responds with the IP address: `142.250.185.46`

#### **Step 6: Resolver Stores Result**
- Resolver receives the IP address
- Stores it in cache memory for future queries
- Returns IP address to user's computer

#### **Step 7: Connection Established**
- User's computer receives the IP address
- Browser connects to google's web server
- google webpage loads

---

## DNS Server Types - Detailed

### 1. Resolver Server (Recursive Resolver)

**Role:** First point of contact; acts on behalf of the client

**Location:** Typically provided by your Internet Service Provider (ISP) or public DNS services

**Function:**
- Receives DNS queries from user computers
- Checks its cache for previously resolved addresses
- If not cached, initiates queries to Root, TLD, and Authoritative servers
- Returns the final IP address to the user
- Caches results for future queries

**Examples:**
- ISP DNS servers (Comcast, Verizon, AT&T)
- Google Public DNS: `8.8.8.8` and `8.8.4.4`
- Cloudflare DNS: `1.1.1.1`
- OpenDNS: `208.67.222.222`

---

### 2. Root Name Servers

**Role:** Top of the DNS hierarchy; starting point for all DNS resolution

**Infrastructure:**
- **13 sets** of root servers worldwide (labeled A through M)
- Each "set" consists of multiple physical servers distributed globally using Anycast routing
- Hundreds of actual physical servers worldwide, but only 13 IP addresses
- Operated by **12 different organizations** including ICANN, Verisign, NASA, US Military, and universities

**Function:**
- Root servers **don't know specific IP addresses** for domains
- Their job is to **direct queries to the appropriate TLD server**
- They maintain a list of all TLD servers (.com, .org, .net, etc.)
- When queried for `google.com`, they respond: "I don't know where google.com is, but I can tell you where to find the .com TLD server"

**Why 13 Sets?**
- Limited by the original DNS packet size (512 bytes)
- Each root server has a unique IPv4 address
- Uses Anycast: same IP address routes to the nearest physical server

**Root Server List:** A.ROOT to M.ROOT


---

### 3. TLD (Top-Level Domain) Name Servers

**Role:** Manages all domains within a specific top-level domain extension

**Types of TLDs:**
- **Generic TLDs (gTLDs):** .com, .org, .net, .info, .biz
- **Country Code TLDs (ccTLDs):** .us, .uk, .in, .ca, .au
- **New gTLDs:** .tech, .app, .dev, .blog, .shop

**Function:**
- TLD servers **don't store the actual IP addresses** of domains
- They maintain information about **which Authoritative Name Servers** manage each domain
- When queried for `google.com`, the .com TLD server responds: "I don't know the IP address for google.com, but Google's Authoritative Name Servers (ns1.google.com, ns2.google.com, etc.) do"

**Management:**
- Different organizations manage different TLDs
- Verisign manages .com and .net
- Public Interest Registry manages .org
- Country authorities manage their ccTLDs (e.g., NIXI manages .in for India)

```
The base url will be splited and referred as the following for resolving.

Eg. sphere.comcast.com

.com -> Domain

comcast.com -> Zone

sphere -> Record
```

**Example Query Response:**

```
Query: "Where is google.com?"
TLD .com Server Response:
"Ask these Authoritative Name Servers:

ns1.google.com

ns2.google.com

ns3.google.com

ns4.google.com"
```


---

### 4. Authoritative Name Servers (Most Detailed)

**Role:** The **ultimate source of truth** for a specific domain; holds all DNS records

#### What Makes Them "Authoritative"?

Authoritative Name Servers are called "authoritative" because they:
- **Own the definitive records** for their domain
- Are the **final authority** - no other server can override them
- Provide **official answers** directly from the domain owner's configuration
- Are **responsible for the accuracy** of all information about their domain

#### What Information Do They Store?

Authoritative Name Servers maintain various **DNS records** for their domain:

| Record Type | Purpose | Example |
|-------------|---------|---------|
| **A Record** | Maps domain to IPv4 address | `google.com → 142.250.185.46` |
| **AAAA Record** | Maps domain to IPv6 address | `google.com → 2607:f8b0:4004:c07::71` |
| **CNAME Record** | Creates alias (canonical name) | `www.google.com → google.com` |

#### Who Manages Authoritative Name Servers?

Several options exist:
1. **Domain Owner:** Large companies like Google, Amazon, Microsoft run their own
2. **DNS Hosting Providers:** Cloudflare, AWS Route 53, Google Cloud DNS
3. **Domain Registrars:** GoDaddy, Namecheap (often include DNS hosting)
4. **Web Hosting Companies:** Often provide DNS services with hosting packages

#### How Authoritative Servers Work

When queried, authoritative servers:
1. **Receive the query** from the Resolver
2. **Look up the requested record** in their database
3. **Return the exact information** (e.g., IP address)
4. **Specify the TTL** (Time To Live) - how long the result should be cached

**Example for google.com:**

```
Query: "What is the A record for google.com?"

Authoritative NS Response:
Domain: google.com
Record Type: A
IP Address: 142.250.185.46
TTL: 300 seconds (5 minutes)
```


## Caching Mechanism

DNS implements caching at multiple levels to improve performance:

```
┌─────────────────────────────────────────┐
│         CACHING HIERARCHY               │
├─────────────────────────────────────────┤
│  Level 1: Browser Cache                 │
│  ↓                                      │
│  Level 2: Operating System Cache        │
│  ↓                                      │
│  Level 3: Resolver Cache (ISP)          │
├─────────────────────────────────────────┤
│  If all caches miss → Full DNS Query    │
└─────────────────────────────────────────┘
```

### Benefits of Caching
- **Faster resolution:** Cached results return immediately
- **Reduced load:** Fewer queries to root, TLD, and authoritative servers
- **Better performance:** Websites load faster on repeat visits

### Cache Example
```
First visit to google.com:
Browser → OS → Resolver → Root → TLD → Authoritative → Returns IP
Time: ~100-200ms

Second visit to google.com (within cache TTL):
Browser → Returns cached IP immediately
Time: ~1-5ms
```

---

## Quick Reference

### DNS Resolution Summary

| Step | Server | Action |
|------|--------|--------|
| 1 | User Computer | Checks local cache |
| 2 | Resolver (ISP) | Checks cache, forwards if needed |
| 3 | Root Server | Directs to TLD server |
| 4 | TLD Server | Directs to Authoritative NS |
| 5 | Authoritative NS | Returns IP address |
| 6 | Resolver | Caches and returns IP |
| 7 | User Computer | Connects to website |

### Key Takeaways

✓ DNS translates domain names to IP addresses  
✓ DNS works hierarchically: Resolver → Root → TLD → Authoritative  
✓ Caching improves performance and reduces queries  
✓ 13 sets of root servers exist worldwide  
✓ Authoritative name servers are the final authority for a domain  

---

## Practical Example with Timeline

```
Query: "What is the IP address of google.com?"

┌──────────────────────────────────────────────────────────────┐
│ t=0ms    User types google.com                               │
│ t=10ms   Query sent to Resolver (ISP)                        │
│ t=20ms   Resolver queries Root Server                        │
│ t=40ms   Root Server responds: "Ask .com TLD"                │
│ t=50ms   Resolver queries .com TLD Server                    │
│ t=70ms   TLD responds: "Ask google.com Authoritative NS"     │
│ t=80ms   Resolver queries google's Authoritative NS          │
│ t=100ms  Authoritative NS responds: "142.250.185.46"         │
│ t=110ms  Resolver returns IP to user & caches result         │
│ t=120ms  Browser connects to 142.250.185.46                  │
│ t=150ms  google webpage loads                                │
└──────────────────────────────────────────────────────────────┘
```

---

## Conclusion

DNS is a critical internet infrastructure that seamlessly translates human-friendly domain names into computer-friendly IP addresses. Through its hierarchical structure and intelligent caching mechanisms, DNS enables billions of internet users to access websites using memorable names instead of numeric addresses.

Without DNS, the internet as we know it would be virtually unusable for everyday users.
