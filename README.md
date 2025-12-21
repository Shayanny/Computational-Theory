# SHA-256 Implementation from Scratch - Computational Theory

**Author:** Shayanny  
**Institution:** Atlantic Technological University - Year 4  
**Module:** Computational Theory  
**December 2025**

## Project Overview

This repository contains my complete implementation of the SHA-256 cryptographic hash algorithm, built from scratch using Python and NumPy for my 4th Year Computational Theory module. Instead of just using pre-built libraries, I implemented every component myself to really understand how cryptographic hashing works.

## What This Project Does

I implemented every piece of SHA-256 according to the FIPS 180-4 specification:
- Bitwise logic functions for cryptographic operations
- Prime-based constant generation from cube roots
- Message padding and block parsing
- Complete hash computation engine
- Password cracking demo with security recommendations

**Live Demo:** The `problems.ipynb` notebook can hash any message and crack weak password hashes.

## Repository Structure
```
Computational-Theory/
├── problems.ipynb          # Main implementation notebook
├── README.md              # This file
├── requirements.txt       # Python dependencies
└── .gitignore            # Git exclusions
```

## Quick Start

### Prerequisites
- Python 3.12+
- Jupyter Notebook or VS Code with Jupyter extension

### Setup

1. **Clone the repository:**
```bash
git clone https://github.com/Shayanny/Computational-Theory.git
cd Computational-Theory
```

2. **Create virtual environment:**
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. **Install dependencies:**
```bash
pip install -r requirements.txt
```

4. **Launch Jupyter:**
```bash
jupyter notebook problems.ipynb
```

5. **Run cells sequentially** - Start from the top and run each cell in order.

## My Implementation Approach

### Problem 1: Binary Operations - Learning by Doing

**Challenge:** I needed to really understand bitwise operations, not just memorize formulas.

**What I did:**
- I wrote each function (Parity, Ch, Maj) with tons of inline comments explaining my thinking
- I created visual test cases so I could see the bit-by-bit transformations happening
- I made sure to understand the difference between ROTR (rotate) and SHR (shift) by working through examples
- I used binary notation (`0b1100`) in my tests because it made everything clearer

**Why this mattered:** These functions are the building blocks of SHA-256's mixing operations. If I didn't get these right, nothing else would work since they depended on each other.

### Problem 2: K Constants - Three Prime Functions

**Challenge:** Extract 32-bit fractional parts from cube roots of 64 primes.

**My approach - I built three different versions:**

1. **`primes_manual(n)`** - My first attempt
   - Checks every divisor from 2 to n-1
   - Really slow but I could see exactly what was happening
   - Helped me understand what actually makes a number prime

2. **`primes_manual_2(n)`** - Getting better
   - I realized I only need to check up to √candidate
   - I skipped even numbers after 2
   - This was about 5-10x faster!

3. **`primes(n)`** - My final version
   - I used NumPy to make it really fast
   - Only checks against primes I've already found
   - This is the one I actually use

**Why I built three versions:**
I wanted to understand the basics of the function and behind the optimizations, not just copy efficient code from Stack Overflow. Each version taught me something new about algorithmic thinking.

### Problem 3: Message Padding - Fighting Edge Cases

**Challenge:** Handle messages of any length and add the right padding. For me this was the hardest problem to understand.

**What I did:**
- I used a generator function because it's memory efficient
- I tested with empty messages, tiny messages, and tricky boundary cases
- I used clear variable names like `BLOCK_SIZE` and `msg_len_bits` instead of cryptic abbreviations

**Where I got stuck:** Understanding when 55 vs. 56 bytes needs different padding logic. I eventually grabbed some paper and worked through examples by hand until it made sense.

### Problem 4: Hash Computation - Putting it all Together
**Challenge:** Combine everything into a working SHA-256 implementation.

**What I did:**
- Implemented the 64-round compression function
- Used the K constants from Problem 2 and functions from Problem 1
- Verified against hashlib and NIST test vectors
- Built a clean `sha256()` wrapper function

**Result:** My implementation produces identical hashes to Python's hashlib for all test cases.


### Problem 5: Password Cracking - My Attack Strategy

**Challenge:** Crack three password hashes and explain how to defend against it.

At first I only implemented a dictionary attack , then expanded the common passwords list but still couldn't find all 3 target hashes so I kept trying out different strategies until I found them all.

**My five-stage plan:**

1. **Stage 1:** Try common passwords (< 1 sec)
   - Found: "password" immediately

2. **Stage 2:** Bigger password list (< 1 sec)
   - Found: "cheese"

3. **Stage 3:** Brute force with lowercase + digits, length 4-6 (~45 min)
   - Found: "cheese" again
   - This took forever but showed me the limits
   - Would take too long to keep increasing the length

4. **Stage 4:** L33tspeak patterns (< 1 sec)
   - Found: "P@ssw0rd"
   - Proved that character substitution is pretty useless

5. **Stage 5:** Rainbow tables (didn't need it)
   - Explained how pre-computed attacks work
   - Checked if the three hashes could be found on it at the very end.

**Performance trick:** I used hashlib for the brute force part because it's 100x faster. My implementation was proven correct, so I didn't need to torture myself with slow code.

## Key Decisions I Made

**1. Why np.uint32 everywhere?**
SHA-256 needs unsigned 32-bit integers. I used `np.uint32` to get automatic modulo 2^32 behavior and avoid weird bugs with negative numbers.

**2. Why so many comments?**
Crypto is hard. I wanted future me (and anyone reviewing this) to understand my thinking, not just see working code.

**3. Why test with "abc"?**
It's the official NIST test vector. If my code matches their answer, I know it's correct.

**4. Why build from scratch?**
You can't really understand SHA-256 by just calling `hashlib.sha256()`. You have to build it yourself.

## What I Learned

**Technical stuff:**
- How bitwise operations actually work in crypto
- Generator functions for handling big data efficiently
- NumPy tricks for performance
- The big difference between fast hashing and secure hashing

**Bigger picture:**
- Why salting stops rainbow table attacks
- Why SHA-256 alone is terrible for passwords
- How tiny input changes create totally different hashes
- Why we need slow algorithms like bcrypt and Argon2

**Professional skills:**
- Writing code that explains itself
- Optimizing with understanding, not just speed
- Using version control properly
- Knowing when to optimize and when not to

## Security Tips

Based on what I learned cracking passwords:

1. **Don't use SHA-256 for passwords**
   - Use bcrypt, scrypt, or Argon2 instead
   - They're intentionally slow and include salting built-in

2. **Always salt your passwords**
   - Give each user a unique random salt
   - Stops rainbow table attacks cold
   - Same password = different hash for each user

3. **Use key stretching**
   - Run the hash 10,000+ times (PBKDF2)
   - Makes attackers' work way harder

4. **Make users pick better passwords**
   - At least 12 characters
   - Check against breach databases
   - Block common patterns and l33tspeak tricks

## Dependencies

- **NumPy** (`pip install numpy`) - For 32-bit integer math
- **hashlib** (built-in) - To check my work
- **itertools** (built-in) - For brute force combinations

## References

**Main sources I used:**
- [FIPS 180-4: Secure Hash Standard](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf) - The official SHA-256 spec
- [NIST SP 800-63B: Digital Identity Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html) - Password security best practices

**Other sources:**
- [Real Python: Python Bytes](https://realpython.com/python-bytes/) - Finally understood byte objects
- [Real Python: Generators](https://realpython.com/introduction-to-python-generators/) - How generators actually work
- [NumPy Documentation](https://numpy.org/doc/stable/) - For uint32 operations
- [SecLists Password Database](https://github.com/danielmiessler/SecLists) - Common password lists

**Tools I used:**
- [CrackStation](https://crackstation.net/) - To see rainbow tables in action
- [PEP 8](https://peps.python.org/pep-0008/) - Python style guide

## How I Tested Everything

**My testing approach:**
- I compared my outputs to official NIST test vectors
- I checked everything against Python's hashlib
- I tested some edge cases (empty messages, boundary lengths)
- I made sure the same input always gives the same output


### Personal Reflection

This project was one of the most challenging but rewarding assignments I've completed at ATU. When I started, bitwise operations felt completely foreign to me , there was a lot I couldn't wrap my head around like why rotating bits was different from shifting them, or how XORing three numbers together could be useful for security.

The moment everything started making sense was during Problem 3 (Padding). I spent hours staring at the FIPS specification, completely confused about why 55-byte and 56-byte messages needed different handling.

Problem 5 was eye-opening in a different way. Cracking "password" in under a second made me realize how vulnerable most people's accounts actually are and why many websites require long passwords with a special character. By doing this brute force can go from taking seconds to hours , to days . Combined by key stretching it could take up to years! I was also surprised when I found out about Rainbow Tables which allowed me to find each of the target hashes in less than a second at the very end. If I were to extend this project further I'd like to include salting , which would make Rainbow Tables completely useless.

Building SHA-256 from scratch rather than just calling `hashlib.sha256()` gave me a much deeper understanding of how cryptographic hashing actually works. I now understand *why* these algorithms are designed the way they are, not just *how* to use them. That understanding feels valuable beyond just this module.

