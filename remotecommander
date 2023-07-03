#!/bin/bash

# Définissez le chemin du fichier contenant la liste des serveurs distants
server_list_file="/etc/remotecommander.conf"

while true; do
  echo -n "Souhaitez-vous saisir les informations d'un autre serveur ? (y|N)  "
  read answer
  case "$answer" in
    [yY]*)

    # Incrémentez la variable de boucle
    ((i++))

    # Demandez à l'utilisateur d'entrer les détails du serveur
    read -p "Entrez le nom du serveur ($i): " server_name
    read -p "Entrez l'IP du serveur ($i): " server_ip
    read -p "Entrez le port du serveur ($i): " server_port
    read -p "Entrez le username du serveur ($i): " server_user
    read -p "Entrez le mot de passe du serveur ($i): " server_password

    # Ajoutez les informations saisies sur le serveur à la liste des serveurs distants
    echo "$server_ip $server_name $server_port $server_user $server_password" >> "$server_list_file"
    ;;
    *)
    break
  esac
done

# Affichez la liste des serveurs distants
echo "Liste des serveurs distants: "
index=0
while read ip_server; do
  index=$((index + 1))
  name=$(echo "$ip_server" | cut -f2 -d" ")
  echo "$index) $name"
done < "$server_list_file"

# Demandez à l'utilisateur à quel(s) serveur(s) envoyer le fichier
read -p "Quel(s) serveur(s) voulez-vous utiliser? (Exemple: 3, 1-5, 1,3,5, 1 3 5, vide pour tous): " server_indexes

# Si l'utilisateur n'a sélectionné aucun serveur, sélectionnez tous les serveurs
if [ -z "$server_indexes" ]; then
  server_indexes=$(seq 1 $index)
fi

# Affichez les actions disponibles
echo "Liste des actions: "
echo "1) Envoyer fichier"
echo "2) Envoyer commande"

# Demandez à l'utilisateur l'action a effectuer
read -p "Que voulez-vous faire?: " server_action

# Effectuez l'action en fonction de la réponse de l'utilisateur
if [ "$server_action" = "1" ]; then

  # Définit l'autocomplétion pour la commande read
  complete -F /etc/profile.d/bash_completion.sh -o nospace -W "" -- read

  # Demande à l'utilisateur le chemin du fichier
  read -e -p "Quel fichier voulez-vous envoyer sur les serveurs distants? (Exemple: /destination/directory/file): " file_path
  if [ -e "$file_path" ]; then
    # Nom du fichier
    file_name=$(basename "$file_path")

    # Demandez à l'utilisateur où déposer le fichier sur les serveurs distants
    read -p "Où voulez-vous déposer le fichier sur les serveurs distants? (Exemple: /destination/directory): " remote_directory

    # Pour chaque serveur sélectionné
    for server_index in $(echo "$server_indexes" | tr ',' '\n'); do
      # Si l'utilisateur a entré une plage de serveurs (par exemple: 1-5)
      if echo "$server_index" | grep -q '-'; then
        # Séparez les limites supérieure et inférieure de la plage
        start=$(echo "$server_index" | cut -d '-' -f 1)
        end=$(echo "$server_index" | cut -d '-' -f 2)

        # Pour chaque serveur dans la plage
        for i in $(seq "$start" "$end"); do
          # Récupérez le serveur sélectionné à partir de la liste des serveurs distants
          ip_server=$(sed "${i}q;d" "$server_list_file" | cut -f1 -d" ")
          port_server=$(sed "${i}q;d" "$server_list_file" | cut -f3 -d" ")
          user_server=$(sed "${i}q;d" "$server_list_file" | cut -f4 -d" ")
          password_server=$(sed "${i}q;d" "$server_list_file" | cut -f5 -d" ")


        # Vérifier si le host est présent dans la liste des known hosts
        if ! (ssh-keygen -F "$ip_server" > /dev/null); then
          # Ajouter le fingerprint SSH du host dans la liste des known hosts
          ssh-keyscan "$ip_server" >> ~/.ssh/known_hosts 2>/dev/null
          echo "Host $ip_server ajouté aux known_hosts"
        fi

          # Utilisez sshpass pour vous connecter au serveur distant et envoyer le fichier
          sshpass -p "$password_server" scp -r -P "$port_server" "$file_path" "$user_server@$ip_server:$remote_directory" 2>/dev/null
          
          # Vérification de la réussite du transfert
          if [ "$?" -eq 0 ]; then
            echo "Le fichier a été transféré avec succès."
          else
            echo "Une erreur s'est produite lors du transfert du fichier."
          fi
        done
      else
        # Récupérez le serveur sélectionné à partir de la liste des serveurs distants
        ip_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f1 -d" ")
        port_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f3 -d" ")
        user_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f4 -d" ")
        password_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f5 -d" ")

        # Vérifier si le host est présent dans la liste des known hosts
        if ! (ssh-keygen -F "$ip_server" > /dev/null); then
          # Ajouter le fingerprint SSH du host dans la liste des known hosts
          ssh-keyscan "$ip_server" >> ~/.ssh/known_hosts 2>/dev/null
          echo "Host $ip_server ajouté aux known_hosts"
        fi

        # Utilisez sshpass pour vous connecter au serveur distant et envoyer le fichier
        sshpass -p "$password_server" scp -r -P "$port_server" "$file_path" "$user_server@$ip_server:$remote_directory" 2>/dev/null

        # Vérification de la réussite du transfert
        if [ "$?" -eq 0 ]; then
          echo "Le fichier a été transféré avec succès."
        else
          echo "Une erreur s'est produite lors du transfert du fichier."
        fi
      fi
    done
  else
    echo "Le fichier n'existe pas."
  fi
else
  # Demandez à l'utilisateur où déposer le fichier sur les serveurs distants
  read -p "Qu'elle commande vouez-vous envoyer sur les serveurs distants? " command_server

  # Pour chaque serveur sélectionné
  for server_index in $(echo "$server_indexes" | tr ',' '\n'); do
    # Si l'utilisateur a entré une plage de serveurs (par exemple: 1-5)
    if echo "$server_index" | grep -q '-'; then
      # Séparez les limites supérieure et inférieure de la plage
      start=$(echo "$server_index" | cut -d '-' -f 1)
      end=$(echo "$server_index" | cut -d '-' -f 2)
      # Pour chaque serveur dans la plage
      for i in $(seq "$start" "$end"); do
        # Récupérez le serveur sélectionné à partir de la liste des serveurs distants
        ip_server=$(sed "${i}q;d" "$server_list_file" | cut -f1 -d" ")
        port_server=$(sed "${i}q;d" "$server_list_file" | cut -f3 -d" ")
        user_server=$(sed "${i}q;d" "$server_list_file" | cut -f4 -d" ")
        password_server=$(sed "${i}q;d" "$server_list_file" | cut -f5 -d" ")

        # Vérifier si le host est présent dans la liste des known hosts
        if ! (ssh-keygen -F "$ip_server" > /dev/null); then
          # Ajouter le fingerprint SSH du host dans la liste des known hosts
          ssh-keyscan "$ip_server" >> ~/.ssh/known_hosts 2>/dev/null
          echo "Host $ip_server ajouté aux known_hosts"
        fi

        # Utilisez sshpass pour vous connecter au serveur distant et envoyer le fichier
        sshpass -p "$password_server" ssh -p "$port_server" "$user_server@$ip_server" "$command_server"
      done
    else
      # Récupérez le serveur sélectionné à partir de la liste des serveurs distants
      ip_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f1 -d" ")
      port_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f3 -d" ")
      user_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f4 -d" ")
      password_server=$(sed "${server_index}q;d" "$server_list_file" | cut -f5 -d" ")

      # Vérifier si le host est présent dans la liste des known hosts
      if ! (ssh-keygen -F "$ip_server" > /dev/null); then
        # Ajouter le fingerprint SSH du host dans la liste des known hosts
        ssh-keyscan "$ip_server" >> ~/.ssh/known_hosts 2>/dev/null
        echo "Host $ip_server ajouté aux known_hosts"
      fi

      # Utilisez sshpass pour vous connecter au serveur distant et envoyer le fichier
      sshpass -p "$password_server" ssh -p "$port_server" "$user_server@$ip_server" "$command_server"
    fi
  done
fi
