
# ManageLocalUser.ps1

## Synopsis
Manage a local user account named 'admin' on Windows 10/Windows 11 systems.

## Description
This script performs the following actions:
- Checks if the user 'admin' already exists.
- If the user exists, it resets the password and ensures the user is a member of the local Administrators group.
- If the user does not exist, it creates the user with the specified password and adds the user to the local Administrators group.
- Operates silently without displaying any notifications.

## Parameters
- **Username**: The username of the local user to be managed. Default is 'admin'.
- **Password**: The password to be set for the user.

## Example
```powershell
.\ManageLocalUser.ps1 -Password (Read-Host -AsSecureString "Enter password")
```

## Script

```powershell
<#
.SYNOPSIS
    Manage a local user account named 'admin' on Windows 10/Windows 11 systems.

.DESCRIPTION
    This script performs the following actions:
    - Checks if the user 'admin' already exists.
    - If the user exists, it resets the password and ensures the user is a member of the local Administrators group.
    - If the user does not exist, it creates the user with the specified password and adds the user to the local Administrators group.
    - Operates silently without displaying any notifications.

.PARAMETER Username
    The username of the local user to be managed. Default is 'admin'.

.PARAMETER Password
    The password to be set for the user.

.EXAMPLE
    .\ManageLocalUser.ps1 -Password (Read-Host -AsSecureString "Enter password")
#>

param (
    [string]$Username = "admin",
    [SecureString]$Password
)

if (-not $Password) {
    $Password = Read-Host -AsSecureString "Enter password"
}

# Convert the secure password to plain text (for demonstration purposes only, not recommended)
$plainPassword = [Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($Password))

# Check if the user already exists
$userExists = Get-LocalUser -Name $Username -ErrorAction SilentlyContinue

if ($userExists) {
    # User exists, reset the password and ensure it is in the Administrators group
    Set-LocalUser -Name $Username -Password $Password
    Add-LocalGroupMember -Group "Administrators" -Member $Username -ErrorAction SilentlyContinue
} else {
    # User does not exist, create the user and add to the Administrators group
    New-LocalUser -Name $Username -Password $Password -PasswordNeverExpires -AccountNeverExpires -UserMayNotChangePassword -ErrorAction SilentlyContinue
    Add-LocalGroupMember -Group "Administrators" -Member $Username -ErrorAction SilentlyContinue
}
```

## Disclaimer
This script is provided as-is without any warranty or guarantee. Use it at your own risk.

## Author
This script was authored by aviado1.
