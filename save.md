## cURL: Save into a file

Save the instructions into a file called "Archlinux": `curl -L https://git.io/Archlinux | sed 's/^#* //;/^!/d;s/^+ //;s/IPv*/# IPv/;s/Misc/# Misc/' > Archlinux`
