Coucou tout le monde :salut_main:
Je sais pas vous mais je galère bien avec les bases de données mais j’ai trouvé un process pour les faire pas trop mal. 
Si mon explication n’est pas claire dites moi et j’essayerai de la modifier.

:danger: A chaque modif de votre base de donnée, faite un petit `rails db:migrate` cette commande signifie juste, j’ai fait des changements rails stp applique les

*I- La modélisation du problème* :loupe_gauche:

Alors généralement je commence par un dessin sur une feuille blanche où j’écris les différents modeles et la relation entre eux (1-1, 1-N, ou N-N). 
`Mais ca veut dire quoi 1-1,1-N ou N-N ? `

- Une relation 1-1 ca veut dire que les deux instances vont ensemble, par exemple dans un monde où les propriétaires ne pourraient avoir qu’une maison on aurait une relation 1-1 entre les propriétaires et les maisons (une maison à un propriétaire, et ce propriétaire la à cette maison la bien précise).
- Une relation 1-N c’est une instance qui possède plusieurs instance de l’autre modèle : Sur mon compte Youtube, j’ai publié pleins de videos, mais toutes ses videos sont sur mon compte Youtube.
- Une N-N c’est le velib : Tous les velibs m’appartiennent mais les vélibs sont aussi utilisés par plein de monde.

Une fois que la modélisation est faite je trace des flèches à double sens entre les différents modeles en precisant le type de relation (1-1, 1-N, N-N).

*II- La modélisation de la relation en Rails* :station:

Alors la c’est assez simple :

Si j’ai une relation 1-1, j’utilise un has_one pour les deux modeles
Si j’ai une relation 1-N, j’utilise un has_many pour la classe qui a N (un docteur qui a N rendez-vous) et la reference sera rajouté dans la table qui a le 1 en relation donc je rajoute une reference dansla table RDV.
Si j’ai une relation N-N, j’utilise soit le `has_many through` (si il y a un intermédiaire) sinon le `has_and_belongs_to_many`

_Petit détail assez important:_
Quand la relation est de type 1-N, le model qui est N prendra un s (un docteur a plusieurs patients ça donne : `has_many :patients `
Par contre le model qui est 1 ne prend pas de s (si il y a un seul docteur dans le cabinet, `belongs_to :doctor` )

*1- Has_many, through *
Si j’utilise `has_many :patients, through: :appointments` ça veut dire qu’on est généralement dans un schéma type :
Doctor  1---N AppointmentN--1 Patient

Cette notation signifie que Doctor à N Appoitment mais Appointment n’a qu’un Doctor. Pour indiquer une relation entre Doctor et Patient je vais utiliser 
dans la class Doctor
```has_many :appointments
has_many :patients, through: appointments```
(Dans cette ordre la c’est important d’indiquer à Rails  la table par laquelle il va devoir passer en premier).
Si je veux indiquer une réciprocité de relation je fais la même chose pour la classe Patient
```has_many :appointments
has_many :doctors, through: appointments ```


*2- Has_and_belongs_to_many *
Alors la c’est simple, je n’utilise pas de table intermédiaire.
Pour mon exemple Velib et Utilisateur. J’aurai deux modeles : `User`et `Bicycle`
J’ai pas besoin d’une troisième table entre les deux (pas de RDV pour prendre un velo donc pas de table intermédiaire).
Il faudra executer la ligne de commande suivante:
`rails g migration CreateJoinTableBicycleUser bicycle user ` (par ordre alphabétique de nom de modele)
Et après pour rajouter des élements `bicycle_234.users << Mathieu` (le s est important ici car on est dans une relation type N-N)
Ca signifie que Mathieu à utilisé le velo avec l’ID 234 mais je peux rajouter d’aures instances et j’aurai tout les utilisateurs du Velo 234

Je sais pas si c’est bien expliqué dites moi dans les commentaires si c’est fouilli ou clair. Au pire je rajouterai des exemples. (modifié)
