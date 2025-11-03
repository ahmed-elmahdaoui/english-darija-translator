# English to Darija Translation API 🌐

[![Java](https://img.shields.io/badge/Java-17-orange.svg)](https://adoptium.net/)
[![Jakarta EE](https://img.shields.io/badge/Jakarta%20EE-10.0-blue.svg)](https://jakarta.ee/)
[![WildFly](https://img.shields.io/badge/WildFly-33.0.2-red.svg)](https://www.wildfly.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> Application web de traduction automatique de l'anglais vers le dialecte marocain (Darija) utilisant l'API Gemini de Google.

## 📋 Table des Matières

- [Aperçu](#aperçu)
- [Architecture](#architecture)
- [Fonctionnalités](#fonctionnalités)
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Configuration](#configuration)
- [Utilisation](#utilisation)
- [Structure du Projet](#structure-du-projet)
- [API Documentation](#api-documentation)
- [Technologies](#technologies)
- [Développement](#développement)
- [Déploiement](#déploiement)
- [Contribuer](#contribuer)

## 🎯 Aperçu

Cette application permet de traduire du texte anglais vers le dialecte marocain (Darija) en temps réel. Elle utilise l'API Gemini 1.5 Flash de Google pour effectuer les traductions et est construite avec Jakarta EE et déployée sur WildFly.

### Caractéristiques principales

- ✅ Traduction instantanée Anglais → Darija
- ✅ Interface web intuitive avec style WhatsApp
- ✅ Système de cache côté client (session PHP)
- ✅ API RESTful pour intégration facile
- ✅ Architecture client-serveur découplée

## 🏗️ Architecture

```
┌─────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│                 │         │                  │         │                 │
│  Client Web     │ ◄────► │  Backend API     │ ◄────► │  Google Gemini  │
│  (PHP + CSS)    │  HTTP  │  (Jakarta EE)    │  HTTPS │  API 1.5 Flash  │
│                 │         │                  │         │                 │
└─────────────────┘         └──────────────────┘         └─────────────────┘
```

### Composants

1. **Frontend** : Interface PHP avec CSS personnalisé
2. **Backend** : API REST Jakarta EE sur WildFly
3. **Service IA** : Google Gemini pour la traduction

## ✨ Fonctionnalités

### Interface Utilisateur
- 💬 Interface type chat (style WhatsApp)
- 🎨 Design moderne et responsive
- 📝 Zone de texte pour saisie anglaise
- 💾 Historique des traductions dans la session
- ⚡ Traduction en temps réel

### Backend
- 🔌 API RESTful avec JAX-RS
- 🔄 Intégration Google Gemini AI
- 📊 Gestion des erreurs robuste
- 🚀 Performance optimisée

## 📦 Prérequis

### Logiciels Requis

```bash
Java JDK 17 ou supérieur
Maven 3.6+
WildFly 33.0.2.Final
PHP 7.4+ (pour le client web)
Apache/Nginx (serveur web)
```

### Clé API Google

Vous devez obtenir une clé API Google Gemini :
1. Visitez [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Créez un nouveau projet
3. Générez une clé API
4. Notez la clé pour la configuration

## 🔧 Installation

### 1. Cloner le Repository

```bash
git clone https://github.com/ahmed-elmahdaoui/english-darija-translator.git
cd english-darija-translator
```

### 2. Configuration de la Clé API

Éditez le fichier `Translation/src/main/java/service/TranslatorResource.java` :

```java
private final String KEY = "VOTRE_CLE_API_GEMINI_ICI";
```

⚠️ **Important** : Ne commitez JAMAIS votre clé API sur GitHub !

### 3. Compiler le Projet

```bash
cd Translation
chmod +x mvnw  # Linux/Mac uniquement
./mvnw clean package
```

### 4. Démarrer le Serveur

```bash
./mvnw wildfly:dev
```

Le serveur démarre sur `http://localhost:8080`

### 5. Configurer le Client Web

```bash
cd ../ProjectWebClient

# Vérifiez que l'URL dans translate_client.php pointe vers votre serveur
# Par défaut: http://localhost:8080/Translation/rest/translate/
```

### 6. Démarrer le Client PHP

```bash
# Avec PHP built-in server
php -S localhost:3000

# OU avec Apache/Nginx
# Placez les fichiers dans votre répertoire web (htdocs, www, etc.)
```

Accédez à l'application : `http://localhost:3000/translate_client.php`

## ⚙️ Configuration

### Variables d'Environnement (Recommandé)

Pour une meilleure sécurité, utilisez des variables d'environnement :

```bash
export GEMINI_API_KEY="votre_cle_api"
```

Modifiez ensuite `TranslatorResource.java` :

```java
private final String KEY = System.getenv("GEMINI_API_KEY");
```

### Configuration du Serveur WildFly

Le projet utilise `standalone-full.xml` par défaut. Modifiez dans `pom.xml` si nécessaire :

```xml
<configuration>
    <server-config>standalone-full.xml</server-config>
</configuration>
```

## 🚀 Utilisation

### Interface Web

1. Ouvrez `translate_client.php` dans votre navigateur
2. Tapez votre texte en anglais dans la zone de texte
3. Cliquez sur "Translate"
4. La traduction en Darija apparaît dans l'historique

### API REST

#### Endpoint de Traduction

```http
GET /Translation/rest/translate/{text}
```

**Paramètres :**
- `text` (PathParam) : Texte à traduire en anglais

**Exemples :**

```bash
# Traduction simple
curl http://localhost:8080/Translation/rest/translate/Hello

# Traduction avec espace (URL encoded)
curl http://localhost:8080/Translation/rest/translate/How%20are%20you

# Sans texte (retourne "لاشيء")
curl http://localhost:8080/Translation/rest/translate/
```

**Réponses :**

```bash
# Succès (200 OK)
"كيفاش داير"

# Erreur serveur (500)
{
  "error": "Internal server error"
}
```

## 📁 Structure du Projet

```
english-darija-translator/
├── Translation/                          # Backend Jakarta EE
│   ├── src/
│   │   └── main/
│   │       ├── java/
│   │       │   └── service/
│   │       │       ├── ServerApp.java           # Configuration JAX-RS
│   │       │       └── TranslatorResource.java  # API REST
│   │       └── webapp/
│   │           ├── WEB-INF/
│   │           │   └── web.xml
│   │           └── index.html
│   ├── pom.xml                          # Configuration Maven
│   ├── mvnw                             # Maven Wrapper (Linux/Mac)
│   └── mvnw.cmd                         # Maven Wrapper (Windows)
│
├── ProjectWebClient/                     # Frontend PHP
│   ├── translate_client.php             # Interface principale
│   └── style.css                        # Styles CSS
│
└── README.md                            # Documentation
```

## 🔌 API Documentation

### TranslatorResource

**Classe :** `service.TranslatorResource`

**Base Path :** `/rest/translate`

#### Méthodes

##### `translate(String text)`

Traduit un texte anglais en Darija.

**Signature :**
```java
@GET
@Path("/{text}")
@Produces(MediaType.TEXT_PLAIN)
public Response translate(@PathParam("text") String text)
```

**Paramètres :**
- `text` : Texte anglais à traduire (PathParam)

**Retour :**
- `200 OK` : Traduction réussie (texte en Darija)
- `500 Internal Server Error` : Erreur lors de la traduction

**Exemple d'utilisation :**
```java
// Appel direct
GET http://localhost:8080/Translation/rest/translate/hello

// Avec JAX-RS Client
Client client = ClientBuilder.newClient();
Response response = client.target("http://localhost:8080/Translation/rest/translate/hello")
                          .request()
                          .get();
String darija = response.readEntity(String.class);
```

## 🛠️ Technologies

### Backend
- **Jakarta EE 10.0** : API pour applications d'entreprise
- **JAX-RS** : Services web RESTful
- **WildFly 33.0.2** : Serveur d'applications
- **Maven** : Gestion des dépendances
- **Jackson** : Parsing JSON

### Frontend
- **PHP 7.4+** : Logique serveur
- **HTML5 / CSS3** : Interface utilisateur
- **cURL** : Communication avec l'API

### Service IA
- **Google Gemini 1.5 Flash** : Modèle de traduction

## 💻 Développement

### Mode Développement

```bash
cd Translation
./mvnw wildfly:dev
```

Le mode `dev` permet :
- ✅ Hot reload automatique
- ✅ Débogage facilité
- ✅ Logs détaillés

### Tests

```bash
# Tester l'endpoint
curl -X GET http://localhost:8080/Translation/rest/translate/test

# Vérifier les logs WildFly
tail -f wildfly-xx.x.x.Final/standalone/log/server.log
```

### Personnalisation

#### Modifier le Prompt IA

Dans `TranslatorResource.java`, personnalisez le prompt :

```java
String body = "{"
    + "\"contents\": [{"
    + "\"parts\": [{"
    + "\"text\": \"Votre instruction personnalisée :\\n'" + text + "'\""
    + "}]"
    + "}]"
    + "}";
```

#### Changer le Style de l'Interface

Éditez `ProjectWebClient/style.css` :

```css
/* Couleur principale */
h1 {
    color: #075e54; /* Vert WhatsApp */
}

/* Bulles de chat */
.chat-bubble.english {
    background-color: #dcf8c6; /* Vert clair */
}
```

## 🚀 Déploiement

### Déploiement sur Serveur

#### 1. Build pour Production

```bash
cd Translation
./mvnw clean package -DskipTests
```

Le fichier `Translation.war` est généré dans `target/`

#### 2. Déployer sur WildFly

**Option A : Interface Web**
1. Accédez à `http://votre-serveur:9990`
2. Onglet "Deployments"
3. Upload `Translation.war`

**Option B : CLI**
```bash
cp target/Translation.war /opt/wildfly/standalone/deployments/
```

**Option C : Maven Plugin**
```bash
./mvnw wildfly:deploy
```

### Déploiement du Client PHP

```bash
# Sur Apache
sudo cp -r ProjectWebClient/* /var/www/html/translator/

# Sur Nginx
sudo cp -r ProjectWebClient/* /usr/share/nginx/html/translator/
```

Modifiez l'URL de l'API dans `translate_client.php` :

```php
$url = 'http://votre-domaine.com:8080/Translation/rest/translate/' . urlencode($text);
```

### Variables d'Environnement Production

```bash
# Sur le serveur
export GEMINI_API_KEY="production_key_here"

# Ou dans systemd service
Environment="GEMINI_API_KEY=production_key_here"
```

## 🔒 Sécurité

### Bonnes Pratiques

1. **Clé API** : Utilisez des variables d'environnement
2. **HTTPS** : Activez SSL/TLS en production
3. **Rate Limiting** : Limitez les requêtes à l'API
4. **Validation** : Validez toujours les entrées utilisateur
5. **CORS** : Configurez les origines autorisées

### Configuration CORS (si nécessaire)

Dans `TranslatorResource.java` :

```java
@GET
@Path("/{text}")
@Produces(MediaType.TEXT_PLAIN)
public Response translate(@PathParam("text") String text) {
    // ... votre code ...
    
    return Response.ok(translatedText)
        .header("Access-Control-Allow-Origin", "http://votre-domaine.com")
        .header("Access-Control-Allow-Methods", "GET, OPTIONS")
        .build();
}
```

## 🐛 Dépannage

### Problèmes Courants

#### Erreur : "Connection refused"
```bash
# Vérifier que WildFly est démarré
curl http://localhost:8080/Translation/rest/translate/test

# Vérifier les logs
tail -f wildfly/standalone/log/server.log
```

#### Erreur : "API Key Invalid"
- Vérifiez votre clé API Google Gemini
- Assurez-vous que l'API est activée dans Google Cloud Console

#### Erreur PHP : "Failed to connect"
```php
// Dans translate_client.php, vérifiez l'URL
$url = 'http://localhost:8080/Translation/rest/translate/';
```

#### Port déjà utilisé
```bash
# Changer le port WildFly
./mvnw wildfly:dev -Djboss.http.port=8081
```

## 📝 Limitations

- ⚠️ Dépend de la disponibilité de l'API Google Gemini
- ⚠️ Limite de requêtes selon votre plan Google Cloud
- ⚠️ La qualité de traduction dépend du modèle Gemini
- ⚠️ Cache uniquement dans la session PHP (non persistant)

## 🤝 Contribuer

Les contributions sont les bienvenues ! Voici comment participer :

1. **Fork** le projet
2. **Créer** une branche (`git checkout -b feature/AmazingFeature`)
3. **Commit** vos changements (`git commit -m 'Add AmazingFeature'`)
4. **Push** vers la branche (`git push origin feature/AmazingFeature`)
5. **Ouvrir** une Pull Request

### Guidelines

- Suivez les conventions Java/PHP
- Ajoutez des tests si possible
- Documentez les nouvelles fonctionnalités
- Mettez à jour le README si nécessaire

## 📄 License

Ce projet est sous licence MIT. Voir le fichier [LICENSE](LICENSE) pour plus de détails.

## 👥 Auteurs

**Votre Nom**
- GitHub: https://github.com/ahmed-elmahdaoui
- Email: ahmed.elmahdawi@usmba.ac.ma

## 🙏 Remerciements

- [Google Gemini API](https://ai.google.dev/) pour le service de traduction
- [Jakarta EE](https://jakarta.ee/) pour le framework
- [WildFly](https://www.wildfly.org/) pour le serveur d'applications
- La communauté open source

## 📚 Ressources Utiles

- [Documentation Jakarta EE](https://jakarta.ee/learn/docs/)
- [WildFly Documentation](https://docs.wildfly.org/)
- [Google Gemini API Docs](https://ai.google.dev/docs)
- [JAX-RS Tutorial](https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/index.html)

---

⭐ Si ce projet vous aide, n'hésitez pas à lui donner une étoile !

Made with ❤️ and ☕
