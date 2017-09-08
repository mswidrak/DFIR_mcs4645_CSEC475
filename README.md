function Get_Time{}

Get-Date
}
Get_Time

function Get_Time_Zone{
[System.TimeZone]::CurrentTimeZone

}
Get-Time_Zone

Function Get_Uptime {
$os = Get-WmiObject win32_operatingsystem -ErrorAction SilentlyContinue
$uptime = (Get-Date) - $os.ConvertToDateTime($os.LastBootUpTime)
Write-Output ("Last boot: " + $os.ConvertToDateTime($os.LastBootUpTime) )
Write-Output ("Uptime   : " + $uptime.Days + " Days " + $uptime.Hours + " Hours " + $uptime.Minutes + " Minutes" )
}

Get_Uptime


function windows_version{
[System.Environment]::OSVersion.Version
}



function System_Hardware_Specs{
## Found this below online, great resource
$computerSystem = Get-CimInstance CIM_ComputerSystem
$computerBIOS = Get-CimInstance CIM_BIOSElement
$computerOS = Get-CimInstance CIM_OperatingSystem
$computerCPU = Get-CimInstance CIM_Processor
$computerHDD = Get-CimInstance Win32_LogicalDisk -Filter "DeviceID = 'C:'"
Clear-Host


Write-Host "System Information for: " $computerSystem.Name -BackgroundColor DarkCyan
(Get-WmiObject Win32_ComputerSystem).Name
(Get-WmiObject Win32_ComputerSystem).Domain
"CPU: " + $computerCPU.Name
"HDD Capacity: "  + "{0:N2}" -f ($computerHDD.Size/1GB) + "GB"
"HDD Space: " + "{0:P2}" -f ($computerHDD.FreeSpace/$computerHDD.Size) + " Free (" + "{0:N2}" -f ($computerHDD.FreeSpace/1GB) + "GB)"
"RAM: " + "{0:N2}" -f ($computerSystem.TotalPhysicalMemory/1GB) + "GB"
"Operating System: " + $computerOS.caption + ", Service Pack: " + $computerOS.ServicePackMajorVersion
"User logged In: " + $computerSystem.UserName
"Last Reboot: " + $computerOS.LastBootUpTime
}

function Get_Process_Info{
Get-Process | Select-Object name,fileversion,productversion,company
}

function_Network_Info_From_Online{
//I found this online. It isn't mine but it's all I could do.
param (
   [array]$arrComputer="$env:computername"
   )

   "`n"

   Write-Host "Name|    NetworkCard                                  | IP          | SUBNET      | GateWay      | MacADD           | DNS "  -ForegroundColor Green

foreach ( $Computer in $arrComputer ) {


   $nwINFO = Get-WmiObject -ComputerName $Computer Win32_NetworkAdapterConfiguration | Where-Object { $_.IPAddress -ne $null } #| Select-Object DNSHostName,Description,IPAddress,IpSubnet,DefaultIPGateway,MACAddress,DNSServerSearchOrder | format-Table * -AutoSize
   #| Select-Object DNSHostName,Description,IPAddress,IpSubnet,DefaultIPGateway,MACAddress,DNSServerSearchOrder
   $nwServerName = $nwINFO.DNSHostName
   $nwDescrip = $nwINFO.Description
   $nwIPADDR = $nwINFO.IPAddress
   $nwSUBNET = $nwINFO.IpSubnet
   $nwGateWay = $nwINFO.DefaultIPGateway
   $nwMacADD = $nwINFO.MACAddress
   $nwDNS = $nwINFO.DNSServerSearchOrder
   #        Server/CompName   |NetworkCard | IPAdress  |  SubnetMask|  Gateway    | MAC Address|   DNS |
   Write-Host "$nwServerName | $nwDescrip | $nwIPADDR | $nwSUBNET | $nwGateWay | $nwMacADD | $nwDNS " | ft *

   }

}
}

function Get_Driver_List{
Get-WindowsDriver -Online -All
}

function Listening_Ports{
Get-NetTCPConnection
}


function Established_Connections{
Get-NetTCPConnection -State Established
}
