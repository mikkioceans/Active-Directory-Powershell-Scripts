# Active-Directory-Powershell-Scripts

This repository contains **PowerShell scripts** to automate common Active Directory (AD) administrative tasks. These scripts demonstrate user account creation, bulk account management, and handling disabled accounts — essential skills for any Windows System Administrator.

---

## 📚 Scripts Included

### 1. `CreateADUser.ps1`
- Automates the creation of a single Active Directory user.
- Parameters typically include:
  - `FirstName`
  - `LastName`
  - `Username`
  - `OU` (Organizational Unit)
- Ensures consistent naming conventions and reduces manual effort.

### 2. `CreateMultipleADUsers.ps1`
- Imports user details from a CSV file.
- Bulk-creates multiple AD users in one run.
- Useful for onboarding scenarios (e.g., adding an entire department).

### 3. `MoveDisabledAccounts.ps1`
- Identifies all **disabled user accounts** in Active Directory.
- Moves them to a specified Organizational Unit (e.g., `DisabledUsers` OU).
- Helps maintain a clean AD structure.

---

## 🚀 Usage

### Prerequisites
- Windows Server with Active Directory module installed.
- Run scripts with **Domain Admin** privileges.

### Example Commands
```powershell
# Run single user creation
#import AD Module

Import-Module ActiveDirectory

#Create an AD user
#get-help New-ADUser

#Grab variables from user - Command Prompt

$firstName = Read-Host -Prompt "Please Enter the First Name"
$Surname = Read-Host -Prompt "Please Enter the  Surname"

New-ADUser `
    -Name  "$firstName $Surname" `
    -GivenName $firstName `
    -Surname $Surname `
    -UserPrincipalName "$firstName.$Surname" `
    -AccountPassword (ConvertTo-SecureString "P@$$w0rd123" -AsPlainText -Force) `
    -Path "OU=Domain Users - OCEANSMAIN-OU,OU=OCEANSMAIN-OU,DC=oceans,DC=local" `
    -ChangePasswordAtLogon $true `
    -Enabled $true

# Run bulk creation (requires CSV input)
#Import ActiveDirectory Module

Import-Module ActiveDirectory

$filePath = Read-Host -Prompt "PLease the path to the csv file"

#Import the csv as an array

$Users = Import-Csv $filePath



foreach ($user in $Users) {

#echo $user.'First Name'
#echo $user.'Organizational Unit'
New-ADUser `
    -Name ($user.'First Name' + " " + $user.'Last Name') `
    -GivenName $user.'First Name' `
    -Surname $user.'Last Name' `
    -UserPrincipalName ($user.'First Name' + "." + $user.'Last Name') `
    -AccountPassword (ConvertTo-SecureString "P@ssw0rd123" -AsPlainText -Force) `
    -Description $user.Description `
    -EmailAddress $user.'Email Address' `
    -Title $user.'Job Title' `
    -OfficePhone $user.'Office Phone' `
    -Path $user.'Organizational Unit' `
    -ChangePasswordAtLogon $true `
    -Enabled ([System.Convert]::ToBoolean($user.Enabled))



}

# Move disabled accounts
#import Active Directory

Import-Module ActiveDirectory

#List all disabled ADUsers

Search-ADAccount -AccountDisabled | Select-Object Name, DistinguishedName

#Move all disabled AD user to disabled users OU

Search-ADAccount -AccountDisabled | Move-ADObject -TargetPath "OU=Disabled Users,OU=OCEANSMAIN-OU,DC=oceans,DC=local"
