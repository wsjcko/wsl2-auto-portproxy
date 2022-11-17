# wsl2-auto-portProxy
wsl2-auto-portProxy(wslpp) is a simple tool for proxying port of linux running in wsl2 (which now use a hyper-v nat network), it automatically scans the port in wls and setup a port proxy in windows host.    

**Note: only port listening at [::] or 0.0.0.0 works, and will only works to  your default  wsl distribution**

## Feature
- [x] TCP port support
- [x] custom port proxy config, support live edit
- [ ] web interface
- [ ] UDP port support


## Requirement
your wsl linux must install the `net-tools` by 
```bash
sudo apt-get install net-tools
```
## Build and install
you can download the bin file(wslpp.exe) in [release](https://github.com/HobaiRiku/wsl2-auto-portproxy/releases).
#### or build wslpp.exe from source
```bash
make build
```
the bin file will be store in dist/wslpp.exe    

#### or install with `go get`
```bash
go get https://github.com/HobaiRiku/wsl2-auto-portproxy
```
and use `wsl2-auto-portproxy.exe` to start proxy

## How it works
wslpp start an interval to get IP address of the nat interface and scan all ports listening at all network in the subsystem, then use golang's `net` to start proxy direct to ports.

## Configuration
Support custom configuration by a json file, which must be placed in `%HOMEPATH%/.wslpp/config.json`, the `.wslpp` dir will be created automatically by wslpp when it runs, but the json file should be created by yourself.    
Example:
/mnt/c/Users/wsjcko/.wslpp/config.json
```json
{
  "onlyPredefined": true,
  "predefined": {
    "tcp": [
      "666:22",
      "21001:21001",
      "11001:11001",
      "9302:9302",
      "9001:9001"
    ]
  },
  "ignore": {
    "tcp": [
      99999
    ]
  }
}
```
* onlyPredefined: If `true`, will only start port defined in `predefined` field.
* predefined: Define the custom port to proxy, "666:22" means `windows(666)->linux(22)`, if undefined, port in windows will follow the same of linux. Must be a string array in the sub field name `tcp`.
* ignore: If defined, will ignore the port in linux. Must be a number array in the sub field name `tcp`. 

**Note: If port is already use by another program in windows, the port will be omitted**

## MyChange 
wsl2-auto-portproxy/lib/service/service.go
func GetLinuxHostPorts:
reg := regexp.MustCompile("(tcp|tcp6|udp)\\s+\\d+\\s+\\d+\\s+(:::|0.0.0.0:)(\\d{2,5})")
......
p, _ := strconv.ParseInt(ret[3], 10, 0)

## MyCMD
netstat -nao | findstr 21001
tasklist | findstr pid
tasklist | findstr wslpp
taskkill /f /t /im wslpp.exe


powershell:
Start-Process -WindowStyle Hidden -FilePath 'D:\wsl2-auto-portproxy\dist\wslpp.exe'

## License
MIT

