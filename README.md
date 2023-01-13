# .NET MAUI Issue 9879

This repository is an example for the issue https://github.com/dotnet/maui/issues/9879 and also https://github.com/microsoft/microsoft-ui-xaml/issues/7845


## Run locally in Visual Studio (no issue)

Clone the project and open `WinUIApp.sln` in Visual Studio. Check, that the
project `WinUIApp` is set as startup project and hit run on `WinUIApp (Package)`. The app
should build and run.


## Building on the CLI

A normalpublish would be like this:

```pwsh
dotnet publish -r win10-x64 -f net6.0-windows10.0.19041.0 /bl .\WinUIApp\WinUIApp.csproj
```

However that gives an error:

> error APPX1101: Payload contains two or more files with the same destination path 'WinUIClassLibrary.pdb'. Source files:  
> error APPX1101: ...\WinUIClassLibrary\bin\Debug\net6.0-windows10.0.19041.0\WinUIClassLibrary.pdb  
> error APPX1101: ...\WinUIClassLibrary\bin\Debug\net6.0-windows10.0.19041.0\win10-x64\WinUIClassLibrary.pdb  

I have had to add a hack to avoid the RID reaching the library:

```xml
<PropertyGroup Condition="'$(RuntimeIdentifierOverride)' != ''">
	<RuntimeIdentifier>$(RuntimeIdentifierOverride)</RuntimeIdentifier>
</PropertyGroup>
```

This is now used like this:

```pwsh
dotnet publish /p:RuntimeIdentifierOverride=win10-x64 -f net6.0-windows10.0.19041.0 /bl .\WinUIApp\WinUIApp.csproj
```

The x64 workflow using my workaround can be found in [.github/workflows/build_windows_x64.yaml](https://github.com/mattleibow/Maui-Issue-9879/blob/main/.github/workflows/build_windows_x64.yaml)
The x86 workflow using my workaround can be found in [.github/workflows/build_windows_x86.yaml](https://github.com/mattleibow/Maui-Issue-9879/blob/main/.github/workflows/build_windows_x86.yaml)

The failing workflow can be found in [.github/workflows/build_windows_x64_nooverride.yaml](https://github.com/mattleibow/Maui-Issue-9879/blob/main/.github/workflows/build_windows_x64_nooverride.yaml)


## Reproduce on local machine

Open Powershell and navigate into the repo folder (after cloning the repo).

Run the following command to build a certificate:

```
New-SelfSignedCertificate `
  -Type Custom `
  -Subject "CN=User Name" `
  -KeyUsage DigitalSignature `
  -FriendlyName "Maui Issue 9879 test Cert Windows" `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3", "2.5.29.19={text}")
```

The publish the MSIX:

```
dotnet publish -r win10-x64 -f net6.0-windows10.0.19041.0 /bl .\WinUIApp\WinUIApp.csproj /p:GenerateAppxPackageOnBuild=true /p:AppxPackageSigningEnabled=true /p:PackageCertificateThumbprint="<YOUR_THUMBPRINT>"
```
