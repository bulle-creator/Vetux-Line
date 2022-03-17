# Vetux-Line

## Contexte

La société de service dans laquelle vous travaillez a comme client l’entreprise VETUX-LINE, createur de ligne de vêtements.

La société VETUX-LINE reçoit de la part de ses partenaires, tous les mois, 2 fichiers clients au format CSV.

Afin d’exploiter ces fichiers partenaires, VETUX-LINE souhaite disposer d’un outil (une application) lui permettant de fusionner ces 2 fichiers en un fichier unique.

La société nous communique des exemples de fichiers reçus. Ces fichiers sont : french-client.csv (~3000 clients) et german-client.csv (~2000 clients). Ces fichiers ont même structure (même type et nombre de colonnes).

Les fichiers reçus contiennent plus d’information que nécessaire. Le fichier résultant de la fusion sera composé d’un sous-ensemble des colonnes existantes (appelé projection) et une sélection de lignes sera effectuée (sélection des personnes majeures uniquement, suppression de doublons…​). La demande du client est détaillée plus loin dans ce document.

Dans un second temps (seconde partie de la mission), le service R&D de la société VETUX-LINE souhaite obtenir ces données sous la forme d’une base de données relationnelle.

## Evils User Story

En tant qu’utilisateur malveillant je veux trouver le mot de passe d’un utilisateur en envoyant de très nombreuses requêtes d’authentification en parallèle pour me connecter à sa session. 

Mesure de protections : Pour empêcher les personnes malveillantes d’avoir accès au mot de passe nous allons crypter le mot de passe. Pour cela je vais utiliser la fonction encodePassword qui va crypter le mot de passe dans la basse de donnés, comme ça même si les personnes malveillantes accèdent à la base de données il ne pourront pas voir le mot de passe qui sera crypter.

```
<?php

namespace App\Controller;

use App\Entity\User;
use Symfony\Component\Security\Core\Encoder\EncoderFactory;
use Symfony\Component\Security\Core\Encoder\MessageDigestPasswordEncoder;
use EasyCorp\Bundle\EasyAdminBundle\Controller\EasyAdminController;

class AdminController extends EasyAdminController
{
    protected function persistUserEntity($user)
    {
        $encodedPassword = $this->encodePassword($user, $user->getPlainPassword());
        $user->setPassword($encodedPassword);

        parent::persistEntity($user);
    }

    protected function updateUserEntity($user)
    {
        $encodedPassword = $this->encodePassword($user, $user->getPlainPassword());
        $user->setPassword($encodedPassword);

        parent::updateEntity($user);
    }

    private function encodePassword($user, $password)
    {
        $passwordEncoderFactory = new EncoderFactory([
            User::class => new MessageDigestPasswordEncoder('sha512', true, 5000)
        ]);

        $encoder = $passwordEncoderFactory->getEncoder($user);

        return $encoder->encodePassword($password, $user->getSalt());
    }
}

```
## Test Unitaire
