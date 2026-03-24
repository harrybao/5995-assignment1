# Session Token Randomness Vulnerability - Proof of Concept
# Vulnerable Code

Located in `sources/com/example/mastg_test0016/Login.java` 

private String generateSessionToken() {
    Random random = new Random();  // NOT cryptographically secure!
    StringBuilder sb = new StringBuilder(16);
    for (int i = 0; i < 16; i++) {
        sb.append("ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789".charAt(random.nextInt(62)));
    }
    return sb.toString();
}


# Why It's Dangerous

1. **Deterministic Output**: `java.util.Random` produces the same sequence for the same seed
2. **Time-Based Seeding**: Default seed is derived from `System.currentTimeMillis()`
3. **Predictable**: Given a token and approximate timestamp, the seed can be brute-forced
4. **Attack Impact**: Once the seed is known, ALL future tokens are predictable

# Attack Scenario

1. Attacker captures a session token (via network sniffing, logs, etc.)
2. Attacker estimates token generation time (±60 seconds)
3. Attacker brute-forces seeds in that time window
4. Seed is found → All future tokens can be generated
5. Attacker impersonates users by forging valid session tokens


# 1. Predictability Demo

Generating tokens with seed 1234567890:
Run 1: ZWN4bGzWFxLnPfjh
Run 2: ZWN4bGzWFxLnPfjh <-- IDENTICAL!
Run 3: ZWN4bGzWFxLnPfjh <-- IDENTICAL!

[!] Notice: ALL THREE ARE IDENTICAL!
[!] This is why java.util.Random is UNSUITABLE for security!

# 2. Brute-Force Attack
[!] Starting brute-force seed recovery...
[*] Known token: 4jf9tBOwoMNJdWNR
[*] Approximate generation time: Tue Mar 24 15:11:20 AEDT 2026
[*] Searching ±60 seconds window...


[???] SEED FOUND! [???]
[+] Seed value: 1774325480538
[+] Seed as date: Tue Mar 24 15:11:20 AEDT 2026
[+] Token successfully reproduced!

[!] Demonstrating future token prediction:
[+] Next token would be: TklSwThg2gz1Q6pw


