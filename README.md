# Vetux-Line

## Contexte

La société de service dans laquelle vous travaillez a comme client l’entreprise VETUX-LINE, createur de ligne de vêtements.

La société VETUX-LINE reçoit de la part de ses partenaires, tous les mois, 2 fichiers clients au format CSV.

Afin d’exploiter ces fichiers partenaires, VETUX-LINE souhaite disposer d’un outil (une application) lui permettant de fusionner ces 2 fichiers en un fichier unique.

La société nous communique des exemples de fichiers reçus. Ces fichiers sont : french-client.csv (~3000 clients) et german-client.csv (~2000 clients). Ces fichiers ont même structure (même type et nombre de colonnes).

Les fichiers reçus contiennent plus d’information que nécessaire. Le fichier résultant de la fusion sera composé d’un sous-ensemble des colonnes existantes (appelé projection) et une sélection de lignes sera effectuée (sélection des personnes majeures uniquement, suppression de doublons…​). La demande du client est détaillée plus loin dans ce document.

Dans un second temps (seconde partie de la mission), le service R&D de la société VETUX-LINE souhaite obtenir ces données sous la forme d’une base de données relationnelle.
## Telechargement d'un fichier

Le didacticiel de téléchargement de fichiers Symfony montre comment télécharger un fichier dans une application Symfony. Dans l’exemple, nous utilisons un formulaire normal pour envoyer le fichier; nous n’utilisons pas de générateur de formulaires.

Nous créons un nouveau projet Symfony et allons dans le répertoire du projet.
```
$ php bin/console --version
Symfony 5.0.8 (env: dev, debug: true)
```
Nous utilisons Symfony 5.0.8.
```
$ composer require annot twig
```
Nous installons le pakcage suivant: et . Ceux-ci sont nécessaires pour créer des itinéraires et des modèles. annotationstwig
```
$ composer require symfony/security-csrf monolog
```
Le package est nécessaire contre les falsifications de requêtes inter-sites et pour la journalisation. symfony/security-csrfmonolog
```
$ composer require maker profiler --dev
```
Pour la phase de développement, nous installons également le maker et le profileur.

##C réation d’une application Symfony
Nous définissons le répertoire où les images seront téléchargées.

####config/services.yaml

```
parameters:
    upload_dir: '../var/uploads'

services:
    # default configuration for services in *this* file
    _defaults:
        autowire: true      # Automatically injects dependencies in your services.
        autoconfigure: true # Automatically registers your services as commands, ...

        bind:
            $uploadDir: '%upload_dir%'
...
```
Puis nous créons un controller Home.
```
$ php bin/console make:controller HomeController
```
#### src/Contrôleur/HomeController.php
```
<?php
namespace App\Controller;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;
class HomeController extends AbstractController
{
    /**
     * @Route("/", name="home")
     */
    public function index()
    {
        return $this->render('home/index.html.twig');
    }
}
```
Il s’agit d’un contrôleur simple qui envoie une vue contenant le formulaire Web à l’utilisateur.

#### templates/home/index.html.twig
```
{% extends 'base.html.twig' %}
{% block title %}Upload file{% endblock %}
{% block body %}
  <form action="{{ path('do-upload') }}" method="post" enctype="multipart/form-data">
        <input type="hidden" name="token" value="{{ csrf_token('upload') }}" />
        <div>
            <label for="myfile">File to upload:</label>
            <input type="file" name="myfile" id="myfile">
        </div>
        <button type="submit">Send</button>
  </form>
{% endblock %}
```
Créé un controller
```
$ php bin/console make:controller UploadController
```
#### src/Contrôleur/UploadController.php
```
<?php
namespace App\Controller;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Routing\Annotation\Route;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use App\Service\FileUploader;
use Psr\Log\LoggerInterface;
class UploadController extends AbstractController
{
    /**
     * @Route("/doUpload", name="do-upload")
     * @param Request $request
     * @param string $uploadDir
     * @param FileUploader $uploader
     * @param LoggerInterface $logger
     * @return Response
     */
    public function index(Request $request, string $uploadDir,
                          FileUploader $uploader, LoggerInterface $logger): Response
    {
        $token = $request->get("token");
        if (!$this->isCsrfTokenValid('upload', $token))
        {
            $logger->info("CSRF failure");
            return new Response("Operation not allowed",  Response::HTTP_BAD_REQUEST,
                ['content-type' => 'text/plain']);
        }
        $file = $request->files->get('myfile');
        if (empty($file))
        {
            return new Response("No file specified",
               Response::HTTP_UNPROCESSABLE_ENTITY, ['content-type' => 'text/plain']);
        }
        $filename = $file->getClientOriginalName();
        $uploader->upload($uploadDir, $file, $filename);
        return new Response("File uploaded",  Response::HTTP_OK,
            ['content-type' => 'text/plain']);
    }
}
```
Si tout se passe bien, nous envoyons un message simple « Fichier téléchargé » au client avec le code de réponse. Response::HTTP_OK

#### src/Service/FileUploader.php
```
<?php
namespace App\Service;
use Symfony\Component\HttpFoundation\File\Exception\FileException;
use Psr\Log\LoggerInterface;
class FileUploader
{
    private $logger;
    public function __construct(LoggerInterface $logger)
    {
        $this->logger = $logger;
    }
    public function upload($uploadDir, $file, $filename)
    {
        try {
            $file->move($uploadDir, $filename);
        } catch (FileException $e){
            $this->logger->error('failed to upload image: ' . $e->getMessage());
            throw new FileException('Failed to upload file');
        }
    }
}
```
Le service déplace le fichier vers le répertoire de téléchargement avec . Lorsque l’opération échoue, nous lançons un fichier . Cela entraînera la production d’une page d’erreur. FileUploadermove()FileException

## Inscription et connection
 Ouvrez une console avec CMD sur Windows ou d’un terminal sous Linux et à l’endroit désiré et créez (si cela n’a pas déjà été fait) un dossier pour vos projets Symfony :
```
 mkdir symfony
```
 Allez dans le répertoire et créez le projet avec la « recette » pour une application Web complète :
 ```
> cd symfony
> symfony new --no-git --full secufony
> cd secufony
```
##### Note : l’instruction « no git » est montrée par la commande symfony new -h ; on peut dans ce projet se passer de la mise en dépôt Git.
Ouvrez PHPStorm sur un le projet par la ligne New Project from Existing Files à partir du répertoire secufony, avec le dernier choix de scénario (pas de serveur Web configuré) ; profitezen pour vérifier l’installation des plugins (File/Settings/Plugins) Symfony, PHPToolbox, PHP Annotations et .env files support :
Changez la ligne dans le fichier .env de la racine au niveau de la variable
```
DATABASE_URL :
DATABASE_URL="mysql://sio:sio@127.0.0.1:3306/secufony?serverVersion=mariadb-10.3.31"
```
##### Note : une seule ligne doit être dé-commentée et renseignée bien sûr avec les bons identifiants… ; 
vous trouverez la version de MariaDB dans la première page de PHPMyAdmin ; si vous êtes sous MySQL, ne mettez que le numéro de version.

Créez la base de donnée secufony par PHPMyAdmin (préférable à la création automatique par Symfony).

 Installez le bundle pour la sécurité des connexions utilisateurs :
secufony> composer require symfony/security-bundle
Toute application liée à des utilisateurs comporte une table les contenant !

Créez l’entité par la « recette » :
```
secufony> php bin/console make:user
```
→ On met Utilisateur au lieu de user (par défaut), version française et cela évite la confusion avec le mot user de la recette… On choisit la propriété username plutôt qu’email comme identifiant unique ; le reste par défaut.
Cette entité se retrouve liée au bundle de sécurité (voir config/packages/security.yaml qui contientcontient tous les paramètres  pour la gestion sécurisée des utilisateurs).

On fait ensuite la migration par l’intermédiaire de Symfony :
```
secufony> php bin/console make:migration
secufony> php bin/console doctrine:migrations:migrate
```
→ Vérifiez la bonne création de la table avec PHPMyAdmin.

Créez à l’aide de la « recette » les formulaires spécifiques à l’authentification :
```
secufony> php bin/console make:auth
```
→ Tapez 1 pour le choix d’authentification, mettez LogInFormAuthenticator pour le nom de classe et
validez le reste.
Cette commande modifie le fichier security.yaml, ajoute un contrôleur, ajoute une classe héritant de
AbstractFormLoginAuthenticator et enfin ajoute un template.

Lancez ensuite le serveur Symfony :
```
secufony> symfony server:start --no-tls
```
→ Vérifiez le bon lancement par l’URL localhost:8000/login.
Pour l’instant, la gestion des utilisateurs n’est pas faite. Il faut utiliser une « recette » de type CRUD.

Créez à l’aide de la « recette » les formulaires spécifiques à l’authentification :

```
secufony> php bin/console make:crud
```
→ Tapez Utilisateur comme choix de nom de classe sur laquelle positionner les opérations CRUD et
validez le reste.
On a donc un nouveau contrôleur nommé UtilisateurController.php.

Modifiez la méthode new dans ce contrôleur :
```
/**
* @Route("/new", name="utilisateur_new", methods={"GET","POST"})
*/
public function new(Request $request, UserPasswordHasherInterface $passwordHasher):
Response
{
 $utilisateur = new Utilisateur();
Page 2 sur 7
Dernière révision du document : 31/10/21
 $form = $this->createForm(UtilisateurType::class, $utilisateur);
 $form→handleRequest($request);
 if ($form->isSubmitted() && $form->isValid()) {
 $entityManager = $this→getDoctrine()->getManager();

 $utilisateur→setPassword(
 $passwordHasher->hashPassword($utilisateur, $utilisateur->getPassword()));
 $entityManager->persist($utilisateur);
 $entityManager->flush();
 return $this->redirectToRoute('utilisateur_index');
 }
 return $this->render('utilisateur/new.html.twig', [
 'utilisateur' => $utilisateur,
 'form' => $form->createView(),
 ]);
}
```
Et ajoutez la librairie nécessaire :
```
 use Symfony\Component\PasswordHasher\Hasher\UserPasswordHasherInterface;
 ```
 Dans le formulaire UtilisateurType.php, ajoutez use Symfony\Component\Form\
Extension\Core\Type\PasswordType; pour avoir le type Symfony du mot de passe caché et
changez les lignes par :
```
 // ->add(‘Roles)
 ->add('password',PasswordType::class)
 ```
On s’occupe maintenant du visuel.
Modifiez le template pour un nouvel utilisateur dans new.html.twig, afin d’ajouter la vérification
du mot de passe :
```
{% extends 'base.html.twig' %}
{% block title %}Nouvel Utilisateur{% endblock %}
{% block body %}
<h1>Création d’un utilisateur</h1>
{{ form_start(form, {'attr': {'id': 'new_edit_utilisateur'}}) }}
{# utilisation de classes bootstrap pour la mise en forme #}
 <div class="row">
 <div class="col-12">
 {{ form_label(form.username) }}
 {{ form_widget(form.username) }}
 </div>
 <div class="col-12">
 {{ form_label(form.password) }}
 {{ form_widget(form.password) }}
 </div>
 <div class="col-12">
 <label for="verifpass">Saisir une seconde fois le mot de passe</label>
 <input type="password" id="verifpass" required>
 </div>
 </div>
 <button class="btn btn-success">{{ button_label|default('Save') }}</button>
 {{ form_end(form) }}
<a href="{{ path('utilisateur_index') }}">retour à la liste</a>
{% endblock %}
```
Il va falloir un script de vérification. Pour être plus rapide, j’ai pris un script JQuery.
Créez un dossier js dans le répertoire public puis dedans un fichier script.js que vous
remplirez avec :
```
$("#new_edit_utilisateur").on('submit', function(){
 if($("#utilisateur_password").val() != $("#verifpass").val()) {
 alert("Les deux mots de passe saisis sont différents");
 alert("Merci de renouveler l'opération");
 return false;
 }
})
```
Ajoutez une route dans le dossier Security pour avoir valider une connexion réussie :
```
 …
 //on renvoie à la liste des utilisateurs
 return new RedirectResponse($this->urlGenerator->generate('utilisateur_index'));
 throw...
 ```
 Il reste à modifier le fichier template de base :
 ```
<!DOCTYPE html>
<html>
 <head>
 <meta charset="UTF-8">
 <title>{% block title %}Secufony{% endblock %}</title>
 {% block stylesheets %}
 <link rel="stylesheet" href="https://bootswatch.com/4/yeti/bootstrap.min.css">
 {% endblock %}
 </head>
 <body>
 <nav class="navbar navbar-light bg-light">
 <a class="navbar-brand">Navbar</a>
 {% if app.user %}
 <div>
 Bonjour {{ app.user.username }} <a class="btn btn-sm btn-danger" href="{{
path('app_logout') }}">Déconnexion</a>
 </div>
 {% else %}
 <div>
 <a class="btn btn-sm btn-primary"
href="{{ path('utilisateur_new') }}">S'inscrire</a>
 <a class="btn btn-sm btn-success" href="{{ path('app_login') }}">Se
connecter</a>
 </div>
 {% endif %}
 </nav>
 <div class="container">
 {% if message is defined %}
 <div class="alert alert-danger">
 {{ message }}
 </div>
 {% endif %}
 {% block body %}
 {% endblock %}
 </div>
 {% block javascripts %}
 <script src="https://code.jquery.com/jquery-3.5.1.min.js"
integrity="sha256-9/aliU8dGd2tb6OSsuzixeV4y/faTqgFtohetphbbj0=" crossorigin="anonymous">
 </script>
 <script src="/js/script.js"></script>
 {% endblock %}
 </body>
</html>
```
 Testez : une inscription, un login et une modification.
La modification du mot de passe d’un utilisateur montre un mot de passe en clair ! Changez cela en
vous inspirant de ce qui est fait dans la méthode new du contrôleur Utilisateur.
Après un login réussi, une déconnexion montre une mauvaise route ! Changez cela pour rediriger la
déconnexion vers la page de login.
