<p align="center"><img src="https://user-images.githubusercontent.com/31022056/192372549-3f4b2e53-8b18-4a0d-ab30-49527dcd255c.png" /></p>

<p align="center">A simple forward auth provider to protect <a href="https://traefik.io/traefik/">Traefik</a> services with user authentication.</p>

<p align="center">
<a href="https://hub.docker.com/r/marvinjwendt/traefik-guardian"><img alt="Docker Cloud Build Status" src="https://img.shields.io/docker/cloud/build/marvinjwendt/traefik-guardian?style=for-the-badge"></a>
<a href="https://hub.docker.com/r/marvinjwendt/traefik-guardian"><img alt="Docker Image Size (tag)" src="https://img.shields.io/docker/image-size/marvinjwendt/traefik-guardian/latest?style=for-the-badge"></a>
<a href="https://hub.docker.com/r/marvinjwendt/traefik-guardian"><img alt="Docker Pulls" src="https://img.shields.io/docker/pulls/marvinjwendt/traefik-guardian?style=for-the-badge"></a>
<a href="https://github.com/MarvinJWendt/traefik-guardian/blob/main/LICENCE"><img alt="GitHub" src="https://img.shields.io/github/license/MarvinJWendt/traefik-guardian?style=for-the-badge"></a>
</p>

![Screenshot](https://user-images.githubusercontent.com/31022056/192390005-428ff759-8a11-4e54-ba97-1c390e4bd1ed.png)

## Features

| Feature                          | Description                                                                                               |
|----------------------------------|-----------------------------------------------------------------------------------------------------------|
| 🧸 Easy to use                   | Dead simple to use! No config files, no external dependencies, no setup. One single command to deploy.    | 
| 🔒 Authentication                | Authenticate users with a password.                                                                       |
| 📝 Authorization                 | Authorize users to use services behind the Traefik proxy.                                                 |
| 🔀 Cross Domain Support          | Authorizes across domains and sub-domains. A single login for everything.                                 |
| 🤖 Header Authorization          | Authorize requests by passing the token in a header, to make guarded API connections possible.            |
| 🚄 Super fast                    | Easily handles hunderts of thousands authorization checks per second.                                     |
| 💙 Conforms to Traefik standards | Traefik Guardian implements Traefik Forward Auth. It also uses the same logging format as Traefik itself. |

## Getting started

### Docker Compose

```yaml
  traefik-guardian: # Your traefik-guardian service
    image: marvinjwendt/traefik-guardian:latest
    environment:
      - AUTH_HOST=auth.test.localhost # Replace with your auth host (e.g.: auth.example.com).
      - PASSWORDS=plaintext:test1234|test1337 # Replace with your passwords. See the docs for more info at: https://github.com/MarvinJWendt/traefik-guardian#password-management
    networks:
      - proxy
    labels:
      - traefik.enable=true
      - traefik.docker.network=proxy
      - traefik.http.routers.auth.entrypoints=web
      - traefik.http.routers.auth.rule=Host(`auth.test.localhost`) || Path(`/traefik-guardian-session-share`) # Replace auth.test.localhost with your auth host defined above.
      - traefik.http.middlewares.traefik-guardian.forwardauth.address=http://traefik-guardian/auth
```

## Valid Tags

| Tag    | Description                                                            |
|--------|------------------------------------------------------------------------|
| latest | The latest stable release.                                             |
| edge   | The latest commit on the main branch.                                  |
| x      | A specific major version. Update all non-breaking changes. (e.g.: `1`) |
| x.x    | A specific minor version. Only update patches (e.g.: `1.2`)            |
| x.x.x  | A specific release. (e.g.: `1.2.3`)                                    |

You can find a full example, including Traefik and a demo service, here: [_examples/full](./_examples/full)

## Configuration

> Environment variables are used to configure Traefik Guardian.

### Environment Variables

| Variable Name            | Description                              | Accepted Values                                 | Default Value                         |
|--------------------------|------------------------------------------|-------------------------------------------------|---------------------------------------|
| `AUTH_HOST`              | The host to use.                         | Any valid host (e.g.: `auth.example.com`)       |                                       |
| `PASSWORDS`              | The passwords that can be used to login. | See [Password Management](#password-management) |                                       |
| `DEBUG`                  | Enable debug mode.                       | `true`, `false`                                 | `false`                               |
| `LOGIN_PAGE_TITLE`       | Title of the login page.                 | Any string.                                     | `Traefik Guardian - Login`            |
| `LOGIN_PAGE_FOOTER_TEXT` | Custom footer text for the login page.   | Any string.                                     | `Copyright © 2022 - Traefik Guardian` |

## Password Management

> Passwords are stored in the `PASSWORDS` environment variable.

The `PASSWORDS` environment variable is separated list of passwords, prepended with the used algorithm. Example: `plaintext:pass1|pass2|pass3`

#### Supported Algorithms

| Algorithm   | Tool to generate hash                                                                                                                                                                                                                                                                                                      |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `plaintext` | No tool needed - just plain text passwords.                                                                                                                                                                                                                                                                                |
| `bcrypt`    | You can use [CyberChef](https://gchq.github.io/CyberChef/#recipe=Bcrypt(10)Find_/_Replace(%7B'option':'Simple%20string','string':'$'%7D,'$$$$',true,false,false,false)&input=WW91ciBQYXNzd29yZA) to generate your bcrypt hash. You need to escape every `$` with another one (the CyberChef link does this automatically). |
| `md5`       | You can use [CyberChef](https://gchq.github.io/CyberChef/#recipe=MD5()&input=WW91ciBQYXNzd29yZA) to generate your md5 hash.                                                                                                                                                                                                                         |
| `sha512`    | You can use [CyberChef](https://gchq.github.io/CyberChef/#recipe=SHA2('512',64,160)&input=WW91ciBQYXNzd29yZA) to generate your md5 hash.                                                                                                                                                                                                              |

## Authorization via Header

> You can authorize requests by passing a password in a header, to make guarded API connections possible.

To authorize requests to an API, you can pass the password in the header.  
The header name is `Guardian-Password` and the value should be one of your configured passwords.

---

> [MarvinJWendt.com](https://marvinjwendt.com) &nbsp;&middot;&nbsp;
> Twitter [@MarvinJWendt](https://twitter.com/MarvinJWendt)
