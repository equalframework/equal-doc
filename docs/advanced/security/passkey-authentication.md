# Passkey authentication

The eQual framework allows authentication using a passkey instead of a password, enhancing security and user convenience.
This approach eliminates the risks associated with password theft, such as phishing attacks or brute force attempts, by relying on cryptographic keys rather than user-generated credentials.
Passkeys can be securely stored on the user's device or hardware token and offer seamless integration with multi-factor authentication methods, ensuring a robust defense against unauthorized access.

## Notes

- The passkey authentication is disabled by default, enable the setting `core.security.passkey_creation` to activate it.
- To test it in development mode, use `localhost` hostname instead of `equal.local`. Passkey authentication in a browser requires HTTPS protocol, but `localhost` is an exception that allows HTTP protocol.

## Sign in workflow

You will find below a diagram that describes a sign-in workflow for user authentication and optional passkey creation.
It begins with the user entering their login credentials and checks if the user exists in the system.
If the user has a passkey, they can choose to use it for authentication, with access granted if the passkey is valid.
If no passkey is used, the workflow proceeds to validate the user's password.
Upon successful password verification, the system may offer the option to create a passkey for future logins.
Users can either create a passkey or skip the setup (once or always). The process concludes with the user being successfully authenticated.

<center><img src="/_assets/uml/passkey_sign_in_workflow.png" /></center>

## Passkey authentication workflows

### 1. If the user has no passkey yet

#### Steps

1. Enter login: _The user provides their username or email._
2. Enter password: _The user enters their password for initial authentication._
3. Prompt to register a passkey: _After successful authentication, the system proposes passkey registration._
    - Options:
        - Register: _The user proceeds to register a passkey. The system generates the required challenge, and the user’s device completes the registration process._
        - Ignore: _The user skips registering a passkey for now. They can be reminded again later._
        - Ignore and do not ask for passkey registration again: _The user opts out of passkey registration permanently, and the system respects this preference._

#### Register passkey technical diagram

<center><img src="/_assets/uml/passkey_register.png" /></center>

The relying party is the eQual backend API, it proposes 2 endpoints for passkey registration:

  - get=user_passkey-register-options
  - do=user_passkey-register

#### Example:

A user logs into their banking app for the first time.
After entering their password, they are prompted to create a passkey for faster and more secure future logins.
If they choose to create it, their device’s fingerprint scanner or security key is registered.

### 2. If the user has a passkey registered

This workflow applies to users with an existing passkey.

#### Steps

1. Enter login: _The user provides their username or email._
2. Propose to authenticate using the passkey: _The system detects the presence of a registered passkey and offers this as the primary option._
    - Options:
        - Use passkey: _The user proceeds with passkey authentication. The system sends the challenge to the user’s device, and the authentication completes upon signature verification._
        - Switch to password: _The user can choose to fall back to password authentication if the passkey method is unavailable (e.g., device not accessible)._

#### Passkey authentication technical diagram

<center><img src="/_assets/uml/passkey_authentication.png" /></center>

The relying party is the eQual backend API, it proposes 2 endpoints for passkey authentication:

- get=user_passkey-auth-options
- do=user_passkey-auth

The authentication options are defined by the following passkey settings:

- Allowed formats: _android-key, android-safetynet, apple, fido-u2f, none, packed, tpm_
- User verification: _required, preferred, discourage_
- Cross-platform: _all, cross-platform, platform_
- Allowed authenticators: _USB, NFC, BLE, Hybrid, Internal_

**Example:**

A returning user opens their shopping app.
They enter their username and are prompted to authenticate using their registered passkey.
With a tap on their phone’s fingerprint scanner, they quickly gain access. Alternatively, they can switch to entering their password if their device is unavailable.

## Controllers

### DATA

#### `user_passkey-register-options`

Returns the data needed for passkey registration:

  - a challenge that the authenticator will have to sign
  - a unique/randomly-generated user handle
  - register options depending on settings:
    - relying party id
    - relying party name
    - is user verification enabled
    - cross-platform option
    - allowed attestation formats

#### `user_passkey-auth-options`

Returns the data needed for passkey authentication:

  - user credential ids
  - register options depending on settings:
    - relying party id
    - relying party name
    - is user verification enabled
    - cross-platform option
    - allowed attestation formats
    - allowed authenticators

### ACTIONS

#### `user_passkey-register`

Registers a passkey to access the eQual application:

  - check that given challenge is correctly signed
  - create a passkey
    - store the given public key
    - store the credential id

#### `user_passkey-auth`

Authenticates using a passkey:

  - check that given challenge is correctly signed
  - check that the given passkey data are matching the database's

It returns an access token cookie on successful authentication.

## Settings

A series of settings allows to configure the passkey registration and authentication processes.

### Propose passkey creation

Toggle to enable or disable passkey creation prompt. This ensures users are offered the ability to create passkeys during their authentication process.

### Passkey relying party ID

The domain name used as the relying party ID. For example "localhost" or "my-app.org" . This acts as the unique identifier for the website or application.

### Passkey relying party name

A user-friendly name for the relying party. For example, "MyApp" or "SecureBank" to help users recognize the entity they are authenticating with.

### Passkey Format Enable

Enable or disable attestation formats supported by WebAuthn during credential creation or authentication.
In the Web Authentication API, attestation refers to the process of providing proof about the authenticity of a credential and its security properties.
This is important for verifying that the credential was created in a secure environment, such as a hardware security module or a trusted execution environment.

Supported formats:

- `android-key`: Used for Android devices with a key-based implementation.
- `android-saftynet`: Enables Google's SafetyNet attestation for Android.
- `apple`: Compatibility with Apple's WebAuthn implementation.
- `fido-u2f`: Universal Second Factor standard for security keys.
- `none`: Disables attestation formats.
- `packed`: Combines multiple attestation techniques into a single format.
- `tpm`: Trusted Platform Module-based attestation.

### Passkey User Verification

Passkey User Verification is a critical step in the Web Authentication (WebAuthn) framework to ensure that only the authorized user can use a credential to authenticate.
It involves verifying the user locally on their device before allowing them to proceed with authentication using a passkey.

Options:

- `required`: Enforces biometric or PIN-based user verification.
- `preferred`: Recommends but does not require verification.
- `discouraged`: Skips user verification during authentication.

### Passkey Cross-Platform

Cross-Platform Passkeys are a feature of the Web Authentication (WebAuthn) API that allow users to use passkeys across devices, operating systems, and platforms.
This capability is made possible by syncing passkeys securely through cloud services, enabling a seamless and passwordless experience regardless of the device a user is on.

Options:

- `all`: Allows both platform-specific and cross-platform authenticators.
- `cross-platform`: Prioritizes cross-platform devices like external security keys.
- `platform`: Limits usage to platform authenticators, such as device-specific biometrics.

### Passkey Authenticator

A Passkey Authenticator is a device or platform that securely stores and manages passkeys, enabling passwordless login through Web Authentication (WebAuthn).
Authenticators are a critical component of the WebAuthn API, as they perform the cryptographic operations required for passkey authentication.

#### Supported authenticators:

- `USB`: Security keys connected via USB.
- `NFC`: Near-field communication-enabled security keys.
- `BLE`: Bluetooth-enabled authenticators.
- `Hybrid`: Authenticators supporting multiple communication methods.
- `Internal`: Built-in authenticators like Touch ID or Windows Hello.



Below are common formats for authenticators and their corresponding `auth_level`:

| **Format**          | **Description**                                            | **auth_level**  | **Justification**                                      |
| ------------------- | ---------------------------------------------------------- | --------------- | ------------------------------------------------------ |
| `android-key`       | Android-secured key stored in hardware (TrustZone).        | **AAL2 / AAL3** | AAL3 if using secure enclaves; otherwise, AAL2.        |
| `android-safetynet` | Android server-side validation without dedicated hardware. | **AAL2**        | Provides guarantees without requiring secure hardware. |
| `apple`             | Apple Secure Enclave with Touch ID/Face ID.                | **AAL2 / AAL3** | AAL3 with biometrics; AAL2 otherwise.                  |
| `fido-u2f`          | FIDO U2F hardware keys (non-biometric).                    | **AAL3**        | Meets AAL3 requirements with phishing-resistant keys.  |
| `none`              | No attestation or unknown authenticator origin.            | **AAL1**        | Minimal guarantees on authentication method.           |
| `packed`            | Embedded attestation with public keys.                     | **Variable**    | May qualify as AAL2 or AAL3 based on underlying setup. |
| `tpm`               | Trusted Platform Module (TPM) for secure key storage.      | **AAL3**        | Ensures hardware-based resistance to key cloning.      |



#### Passkey managers supported by eQual
* Apple: integrated into iCloud Keychain (passkey generation and usage).
* Bitwarden: open-source password manager, allowing secure storage and synchronization of passkeys.
* Google Account: authentication with compatible devices through password& passkeys.
* Microsoft Hello: integrated into Windows using biometric methods or a secure PIN.

