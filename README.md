# AWS KMS Bring Your Own Key (BYOK) :

## Prerequisite: 

Should have a Key Material or you can generate new key material Using Open SSL.
> [!CAUTION]
> Avoid using Openssl for genrating key material for Production Workloads.Better use some other tool.

## Openssl :
Generate a 256-bit symmetric key and save it in a file named PlaintextKeyMaterial.bin
### Excecute Command :
> openssl rand -out PlaintextKeyMaterial.bin 32


## Step 1: Create an AWS KMS key without key material :

1.Go to Console and switch to AWS KMS Service
   - Click Customer Managed Keys
   - Create Key
   - Key type - Symmetric
   - Key usage - Encrypt and Decrypt
   - Advanced – External (Import Key Material) and click acknowledgement
   - Regionality Single or Multi
   - Give Alias name and choose Key administrators
   - Key Users – Choose user
   - Finish
> [!IMPORTANT]
>  Check wheather KMS policy has kms:ImportKeyMaterial action for the User
  
## Now You will have a page to Download Wrapping public key and import token
Download the file and unzip it.
You will have two binary files of WrappingPublicKey.bin and ImportToken.bin

> [!NOTE]
> This wrapping public key and import token will expire in 24 hours.So before that Wrap your Key Material and Upload in KMS.

### Encrypting key material with OpenSSL
Now we have to encrypt our Plain Key material with Wrapping Key provided by AWS.

## Excecute Command:
> openssl pkeyutl -encrypt -in PlaintextKeyMaterial.bin -out EncryptedKeyMaterial.bin -inkey WrappingPublicKey.bin -keyform DER -pubin -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha1

Replace path in command as below :
  - -in Genrated Key Material
  - -out Output path to get encrypted Key material
  - -inkey WrappingKey path downloaded from AWS
We have Encrypted key material.

# Import Our Key Material to AWS KMS
Go to AWS Console :
- Choose key and select Key Material
- Select Import Key Material
- Next and Upload Wrapped key material and Import token.
- Set Expiration option Enable and choose time period

> [!IMPORTANT]
> If you choose Expiration then we have to ReWrap Key by genrating a new key encrypted material and Reupload.Old uploaded file will be expired.