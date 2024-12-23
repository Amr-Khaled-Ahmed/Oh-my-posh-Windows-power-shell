# Oh-My-Posh Windows PowerShell Setup

## 1. Initialize Oh My Posh with Default Theme

```powershell
# Example themes already initialized (Uncomment as needed)
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\json.omp.json' | Invoke-Expression
oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\cloud-native-azure.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\jandedobbeleer.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\chips.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\dracula.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\if_tea.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\sonicboom_dark.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\atomic.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\blue-owl.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\bubbles.omp.json' | Invoke-Expression
# oh-my-posh init pwsh --config 'C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\devious-diamonds.omp.json' | Invoke-Expression


# Import functionality
Import-Module PSFzf
Import-Module PSWindowsUpdate
# Import-Module PSColor
Import-Module PSReadLine
Set-PSReadLineOption -PredictionSource History
Import-Module -Name Terminal-Icons


# Define a function to switch themes dynamically
function Set-PoshTheme {
    param (
        [Parameter(Mandatory)]
        [string]$Theme
    )

    $themePath = "C:\Users\Amr khaled\AppData\Local\Programs\oh-my-posh\themes\$Theme.omp.json"
    if (Test-Path $themePath) {
        oh-my-posh init pwsh --config $themePath | Invoke-Expression
        Write-Host "Switched to theme: $Theme" -ForegroundColor Green
    } else {
        Write-Host "Theme '$Theme' not found!" -ForegroundColor Red
    }
}

# Menu to switch themes dynamically with fuzzy search
function Choose-PoshTheme {
    $themes = @(
        "jandedobbeleer",
        "chips",
        "dracula",
        "if_tea",
        "sonicboom_dark",
        "atomic",
        "blue-owl",
        "bubbles",
        "devious-diamonds",
        "cloud-native-azure",
        "jandedobbeleer",
        "mario",
        "powerline",
        "paradox",
        "pure",
        "snowy-night",
        "tango",
        "vscode",
        "powerline-v2",
        "crystal",
        "horizon",
        "sphinx",
        "old-skool",
        "seabird",
        "new-age",
        "macchiato",
        "react",
        "dracula-dark",
        "frodo",
        "night-owl"
    )

    Write-Host "Available Themes:" -ForegroundColor Cyan
    $themes | fzf --preview 'echo {}' --preview-window=up:20 | ForEach-Object {
        Set-PoshTheme -Theme $_
    }
}



# Aliases for common commands
Set-Alias posh-theme Choose-PoshTheme
Set-Alias tt tree
Set-Alias gnip Get-NetIPAddress
Set-Alias vim nvim
Set-Alias alies Get-Alias
Set-Alias edit notepad
Set-Alias pscan Test-NetConnection  # Alias for testing network connections

# Functions for extended functionality
Function getip { Get-NetIPAddress | Where-Object { $_.AddressFamily -eq 'IPv4' } }
Function ll { Get-ChildItem -Force | Sort-Object Name }
Function lsr { Get-ChildItem -Recurse -Force }
Function gs { Get-Service }
Function md { New-Item -ItemType Directory }
Function netinfo { Get-NetAdapter | Select-Object Name, Status, MacAddress, LinkSpeed }
Function pingtest { Test-Connection -Count 4 }
Function ports { Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State }
Function size { Get-ChildItem -Recurse | Measure-Object -Property Length -Sum | Select-Object Count, Sum }
Function tree { Get-ChildItem -Recurse -Force | Format-Table FullName, Attributes }
Function listusers { Get-LocalUser }
Function groups { Get-LocalGroupMember -Group "Administrators" }
Function clsrv { Clear-DnsClientCache }
Function cpuinfo { Get-WmiObject Win32_Processor | Select-Object Name, NumberOfCores, MaxClockSpeed }
Function raminfo { Get-WmiObject Win32_PhysicalMemory | Select-Object Manufacturer, Capacity, Speed }
Function sysinfo { Get-ComputerInfo | Select-Object CsName, WindowsVersion, OsArchitecture, CsManufacturer, CsModel }




#seach using Fuzzy finder
function search {
    Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf
}

function fuzzysearch {
    Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf --preview="type {}"
}
Set-Alias fs fuzzysearch

function fuzzydirs {
    Get-ChildItem -Path C:\ -Recurse -Directory -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf
}
Set-Alias fd fuzzydirs

function fuzzyext {
    param (
        [string]$ext = "*.txt"
    )
    Get-ChildItem -Path C:\ -Recurse -File -Include $ext -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf
}
Set-Alias fx fuzzyext

function openfile {
    Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf | ForEach-Object { notepad $_ }
}
Set-Alias of openfile

function searchpreview {
    Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf --preview="if ($env:OS -eq 'Windows_NT') { type {} | Out-String } else { cat {} }"
}
Set-Alias fsp searchpreview

function copyfilepath {
    Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf | Set-Clipboard
}
Set-Alias fcfp copyfilepath

function recentfiles {
    param (
        [int]$days = 7
    )
    Get-ChildItem -Path C:\ -Recurse -File -ErrorAction SilentlyContinue | Where-Object { $_.LastWriteTime -ge (Get-Date).AddDays(-$days) } | ForEach-Object { $_.FullName } | fzf
}
Set-Alias frf recentfiles

function searchtext {
    param (
        [string]$query
    )
    rg --files-with-matches $query | fzf
}
Set-Alias fst searchtext

function openfolder {
    Get-ChildItem -Path C:\ -Recurse -Directory -ErrorAction SilentlyContinue | ForEach-Object { $_.FullName } | fzf | ForEach-Object { Start-Process explorer $_ }
}
Set-Alias fofd openfolder
