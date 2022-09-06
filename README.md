# Maui-Issue-9879

This repository is an example for the issue https://github.com/dotnet/maui/issues/9879

## Run locally in Visual Studio (no issue)

Clone the project and open `TestIssue9879/TestIssue9879.sln` in Visual Studio. Check, that the project `MauiApplication` is set as startup project and hit run on `Windows Machine`. The app should build and open up:

![image](https://user-images.githubusercontent.com/37712964/188624641-180afd55-9457-4e28-ae8e-2f469cd4b30e.png)

### Common issue

If the following appears:

![image](https://user-images.githubusercontent.com/37712964/188624732-73fdf325-7740-48ea-985a-e083a2cecc77.png)

Open the configuration manager:

![image](https://user-images.githubusercontent.com/37712964/188624823-e8b682a3-51c7-425c-bccd-4712ae632a5c.png)
 
 And uncheck the deployment for `MauiClassLibrary`:
 
 ![image](https://user-images.githubusercontent.com/37712964/188624889-a48465c7-39cd-45a1-8d94-56d114063847.png)

### Information for reproduction

This was tested on `Visual Studio Community 2022 Preview` in version `17.4.0 Preview 1.0` on a Windows 10 laptop.

## Build app with `dotnet publish` (issue)

To make it easier to reproduce, I added two workflows with the problematic commands:

### For x64 build

The workflow can be found [here](https://github.com/Tardigrade42/Maui-Issue-9879/blob/main/.github/workflows/build_windows_x64.yaml)  

The failed run with the issue can be found [here](https://github.com/Tardigrade42/Maui-Issue-9879/runs/8205713407?check_suite_focus=true)  

### For x86 build

The workflow can be found [here](https://github.com/Tardigrade42/Maui-Issue-9879/blob/main/.github/workflows/build_windows_x86.yaml)  

The failed run with the issue can be found [here](https://github.com/Tardigrade42/Maui-Issue-9879/runs/8205713410?check_suite_focus=true)  

### Reproduce on local machine

Open Powershell and navigate into the repo folder (after cloning the repo). Run the following command to build a certificat:
```
New-SelfSignedCertificate -Type Custom `
                          -Subject "CN=User Name" `
                          -KeyUsage DigitalSignature `
                          -FriendlyName "Maui Issue 9879 test Cert Windows" `
                          -CertStoreLocation "Cert:\CurrentUser\My" `
                          -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3", "2.5.29.19={text}")
```
Now to build the x86 msix run:
```
dotnet publish TestIssue9879/MauiApplication/MauiApplication.csproj -c Release -f:net6.0-windows10.0.19041.0 /p:GenerateAppxPackageOnBuild=true /p:Platform=x86 /p:AppxPackageSigningEnabled=true /p:PackageCertificateThumbprint="<YOUR_THUMBPRINT>" /p:RuntimeIdentifier=win10-x86
```

To build the x64 msix run:
```
dotnet publish TestIssue9879/MauiApplication/MauiApplication.csproj -c Release -f:net6.0-windows10.0.19041.0 /p:GenerateAppxPackageOnBuild=true /p:Platform=x64 /p:AppxPackageSigningEnabled=true /p:PackageCertificateThumbprint="<YOUR_THUMBPRINT>"
```


