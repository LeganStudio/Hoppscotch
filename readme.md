# Hoppscotch

Note: All attached commands must be executed in the same folder where .env, hoppscotch.conf and docker-compose.yml are located.

## Configure Cloudflare

### Configure authentication methods
1. Sign in to [Cloudflare](https://dash.cloudflare.com/)
2. Zero Trust Option
3. Settings -> Authentication
4. Go to Login methods and click on Add new.
5. Select an identity provider
6. Follow the setup steps

### Configure access policy (optional)
1. Sign in to [Cloudflare](https://dash.cloudflare.com/)
2. Zero Trust Option
3. Access -> Policies
4. Click on Add a policy
5. Add configuration

```
Basic Information:
  Policy name: Domain email
  Action: Allow
  Session duration: Same as application session timeout

Add rules
  Selector: Emails ending in
  Value: @<DOMAIN>
```

### Create tunnel
1. Sign in to [Cloudflare](https://dash.cloudflare.com/)
2. Zero Trust Option
3. Networks -> Tunnels
4. Click on Add a tunnel
5. Select Cloudflared
6. Add a published application route

```
Tunnel name: hoppscotch-tunnel

Hostname:
  Subdomain: <SUBDOMAIN>
  Domain: <DOMAIN>

Service: 
  Type: http
  Url: hoppscotch-nginx
```

7. Copy tunnel token

### Configure application
1. Sign in to [Cloudflare](https://dash.cloudflare.com/)
2. Zero Trust Option
3. Access -> Applications
4. Click on Add an application

```
Application name: hoppscotch-app

Hostname:
  Subdomain: <SUBDOMAIN>
  Domain: <DOMAIN>
```

5. Add the access policy

## Configure variables

### Create encryption key

```
openssl rand -base64 32 | cut -c1-32
```

### Add variables to .env

```
nano .env
```

## Configure Nginx

1. Open the configuration file.

```
nano hoppscotch.conf
```

2. Replace the variables: 
  - SUBDOMAIN
  - DOMAIN

## Deployment

### Starting Nginx

```
sudo docker compose run --rm nginx-init
```

### Container deployment

```
sudo docker compose up -d
```

### Database migration

```
sudo docker compose run --rm migrate
```

### Restart backend

```
sudo docker restart hoppscotch-backend
```

## Configure Admin

1. Entrar a https://<SUBDOMAIN>.<DOMAIN>.com/admin
2. Select OAuth Providers
3. Activate the desired providers
4. Add credentials

### GitHub Authentication
[GitHub documentation](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/creating-an-oauth-app)

#### Scope

```
user:email
```

### Microsoft Authentication
[Microsoft Documentation](https://learn.microsoft.com/en-us/entra/identity-platform/scenario-web-app-sign-user-app-configuration?tabs=nodejs#register-an-app-by-using-the-azure-portal)

#### Scope

```
openid profile email offline_access User.Read
```
