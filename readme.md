## Mise en place de LexikJWT

### Installez JWT:
`composer require lexik/jwt-authentication-bundle`
<br>

### Générer les clés publique et privée:
Dans  git bash, <br>
- `openssl genpkey -out config/jwt/private.pem -aes256 -algorithm rsa -pkeyopt rsa_keygen_bits:4096`, permet de générer la clé privée;

- `openssl pkey -in config/jwt/private.pem -out config/jwt/public.pem -pubout`, permet de générer la clé public.

Il faut bien utiliser la même passphrase pour les deux commandes.
<br>

### Configurer JWT:
- Dans le fichier .env.local,<br>
###> lexik/jwt-authentication-bundle ###

JWT_SECRET_KEY=%kernel.project_dir%/config/jwt/private.pem
JWT_PUBLIC_KEY=%kernel.project_dir%/config/jwt/public.pem
JWT_PASSPHRASE=fdd719e8855fdf770a5141fd0afb817b

###< lexik/jwt-authentication-bundle ###
<br>
<br>Changer la JWT_PASSPHRASE par celle saisie pour générer les clés.
<br>

- Indiquer à Symfony quelle est l’URL qui va être utilisée pour l’authentification, et lui dire également que nous 
voulons que ce soit LexikJWT qui s’occupe de tout.

Pour cela, il faut aller dans le fichier security.yaml:
<br>
Commenter la partie "main" et ajouter ce ci dans la partie "firewalls":<br>
`login:
    pattern: ^/api/login
    stateless: true
    json_login:
        check_path: /api/login_check
        success_handler: lexik_jwt_authentication.handler.authentication_success
        failure_handler: lexik_jwt_authentication.handler.authentication_failure
api:
    pattern: ^/api
    stateless: true
    jwt: ~`
<br>
<br>
Modifier le acces_controle:<br>
`access_control:
    - { path: ^/api/login, roles: PUBLIC_ACCESS }
    - { path: ^/api, roles: IS_AUTHENTICATED_FULLY }`

Enfin, modifier le fichier config\routes.yaml en ajoutant:<br>
`api_login_check:
    path: /api/login_check`

### Testez l’authentification
Dans Postman, faire une requête post sur cette url `https://127.0.0.1:8000/api/login_check`.<br>
Préciser le "Content-Type" -> application/json