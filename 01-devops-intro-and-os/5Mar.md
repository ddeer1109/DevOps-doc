## Add user
useradd -u UID -ms /bin/bash NAME
## Add group and assign user to group
addgroup NAME --gid GID
adduser USER GROUP
## Unassign user from group and delete group
gpasswd -d USER GROUP
delgroup NAME


# Tworzenie nowego użytkownika
sudo useradd -m -s /bin/bash developer
# Tworzenie grupy i dodawanie użytkownika
sudo groupadd developers
sudo usermod -aG developers developer


oprocz swojego uzytkownike , 
3 innych uzytkownikow, 
dwie grupy g1 dla wszystkich , g2 1 or 2 users
id user



Zadania
Zadanie 2: Praca z LVM

sudo su

dwa katalogi 1,2 
wlasciciel - main 
dwa katalogi wlasciciel grupowy  -> 775

log in to given user and test above 



exercise chmod, firewall 



udowodnij, że ... pytania

PD - Przygotuj 3 innych użytkowników, zrób 2 grupy. W pierwszej przypisz wszystkich 3 użytkowników. W drugiej grupie przypisz tylko 1 lub 2 użytkowników. Do sprawdzenia użyj id USER. Utwórz 2 katalogi, każdego właścicielem jesteśmy my, ale właścicielem grupowym jest grupa pierwsza i druga. Sprawdźmy jakimi komendami możemy zalogować się na danego użytkownika. chmod 775 na katalogi.