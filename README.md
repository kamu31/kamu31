import subprocess
import os
import sys
import re
from datetime import datetime

# Vérifie si le script est exécuté sur Kali Linux
def check_kali():
    if not os.path.exists("/etc/kali-release"):
        print("[-] Ce script est conçu pour Kali Linux. Exécution sur un autre système détectée.")
        sys.exit(1)

# Exécute une commande système et retourne la sortie
def run_command(command):
    try:
        result = subprocess.run(command, shell=True, capture_output=True, text=True)
        if result.stderr:
            print(f"[-] Erreur: {result.stderr}")
        return result.stdout
    except Exception as e:
        print(f"[-] Erreur lors de l'exécution de la commande: {e}")
        return None

# Scan réseau de base avec Nmap
def network_scan(target, aggressive=False):
    print(f"\n[+] Lancement du scan réseau sur {target}")
    print(f"Heure de début: {datetime.now()}")
    
    if aggressive:
        command = f"nmap -A -T4 {target} -oN nmap_scan.txt"
    else:
        command = f"nmap -sS -T4 {target} -oN nmap_scan.txt"
    
    output = run_command(command)
    if Aggressive Scanoutput:
        print("\nRésultats du scan Nmap:")
        print(output)
    return output

# Recherche d'informations WHOIS
def whois_lookup(target):
    print(f"\n[+] Recherche d'informations WHOIS pour {target}")
    output = run_command(f"whois {target}")
    if output:
        print("Résultats WHOIS:")
        print(output)

# Test de vulnérabilités HTTP avec Nikto
def nikto_scan(target):
    print(f"\n[+] Lancement du scan Nikto sur {target}")
    command = f"nikto -h {target} -output nikto_scan.txt"
    output = run_command(command)
    if output:
        print("Résultats Nikto:")
        print(output)

# Enumération DNS simple
def dns_enum(target):
    print(f"\n[+] Enumération DNS sur {target}")
    command = f"dnsenum {target} > dnsenum_scan.txt"
    output = run_command(command)
    if output:
        print("Résultats DNS:")
        print(output)

# Scan avec Metasploit (recherche d'exploits basée sur Nmap)
def metasploit_scan(target):
    print(f"\n[+] Lancement de Metasploit pour {target}")
    
    # Crée un fichier de commandes temporaires pour msfconsole
    msf_commands = f"""
    use auxiliary/scanner/portscan/tcp
    set RHOSTS {target}
    set THREADS 10
    run
    exit
    """
    
    with open("msf_script.rc", "w") as f:
        f.write(msf_commands)
    
    # Lance Metasploit avec le script
    command = "msfconsole -q -r msf_script.rc > metasploit_scan.txt"
    output = run_command(command)
    if output:
        print("Résultats Metasploit (voir metasploit_scan.txt pour détails):")
        print(output)
    
    # Nettoyage
    os.remove("msf_script.rc")

def main():
    print("=== Outil de Pentest Automatique pour Kali Linux ===")
    check_kali()
    
    # Demande de la cible
    target = input("Entrez l'IP ou le domaine à tester (ex: 192.168.1.1 ou example.com): ").strip()
    
    # Options de scan
    aggressive = input("Voulez-vous un scan agressif (plus détaillé mais plus détectable) ? (o/n): ").lower() == 'o'
    
    # Menu des tâches
    print("\nChoisissez les actions à effectuer:")
    do_nmap = input("1. Scan réseau avec Nmap ? (o/n): ").lower() == 'o'
    do_whois = input("2. Recherche WHOIS ? (o/n): ").lower() == 'o'
    do_nikto = input("3. Scan Nikto (HTTP) ? (o/n): ").lower() == 'o'
    do_dns = input("4. Enumération DNS ? (o/n): ").lower() == 'o'
    do_msf = input("5. Scan Metasploit (portscan TCP) ? (o/n): ").lower() == 'o'
    
    # Exécution des tâches sélectionnées
    if do_nmap:
        network_scan(target, aggressive)
    if do_whois:
        whois_lookup(target)
    if do_nikto:
        nikto_scan(target)
    if do_dns:
        dns_enum(target)
    if do_msf:
        metasploit_scan(target)
    
    print(f"\nScan terminé à: {datetime.now()}")
    print("Résultats sauvegardés dans: nmap_scan.txt, nikto_scan.txt, dnsenum_scan.txt, metasploit_scan.txt (si applicable)")

if __name__ == "__main__":
    try:
        # Vérifie les privilèges root
        if os.geteuid() != 0:
            print("[-] Ce script doit être exécuté avec les privilèges root (sudo)")
            sys.exit(1)
        main()
    except KeyboardInterrupt:
        print("\n[-] Scan interrompu par l'utilisateur")
        sys.exit(0)
    except Exception as e:
        print(f"[-] Erreur inattendue: {e}")
        sys.exit(1)



## Hi there 👋

<!--
**kamu31/kamu31** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
