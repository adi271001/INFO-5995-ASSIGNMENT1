# INFO5995 Assignment 1: AI-Assisted Cryptographic Vulnerability Discovery

This project contains the security analysis and findings for the `a1_case1 (1).apk` Android application. The audit focuses on identifying cryptographic weaknesses and insecure randomness.

## Project Structure

- `AssignmentRequirements/`: Original assignment specifications and the target APK.
- `decompiled/`: The source code and resources extracted from the APK using `jadx`.
- `findings/`: Contains the analysis results, report, and POC.
  - `report.tex`: The security audit report in USENIX LaTeX format.
  - `ai-log.txt`: A log of AI-assisted steps and tools used.
  - `SessionTokenPOC.java`: A Proof-of-Concept demonstrating the predictability of the session token.
- `tools/`: Local installation of `jadx` used for decompilation.

## Identified Vulnerability

The primary vulnerability discovered is **Insecure Randomness in Session Token Generation** located in `com.example.mastg_test0016.Login.generateSessionToken()`.

The application uses `java.util.Random` to generate 16-character session tokens. Because `java.util.Random` is a deterministic PRNG and not cryptographically secure, an attacker who can estimate the seeding time (typically system time) can predict or regenerate valid session tokens to hijack user sessions.

## How to Run the POC

To verify the predictability of the random number generator used in the app, you can compile and run the provided Java POC:

```bash
# Compile the POC
javac findings/SessionTokenPOC.java

# Run the POC
java -cp findings SessionTokenPOC
```

The output will show that two separate calls to the generator using the same seed produce identical "random" tokens.

## Mitigation

The vulnerability should be mitigated by replacing `java.util.Random` with `java.security.SecureRandom`, which is designed for security-sensitive applications and is resistant to seed prediction.
