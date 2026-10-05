# Plateforme Anime Cocokalo

![Logo Cocokalo](public/images/project-logo.png)

#### Description
Une plateforme vidéo anime open-source avec un design d'interface inspiré de Bilibili. Uniquement à des fins d'apprentissage et de recherche.

#### Avis de droit d'auteur
1. Ceci est un projet d'apprentissage open-source sans but commercial
2. L'utilisation de Cocokalo à des fins non académiques est strictement interdite
3. Tout contenu anime utilisé dans ce projet appartient à ses créateurs originaux
4. N'utilisez pas ce projet pour stocker ou distribuer du contenu protégé par des droits d'auteur non autorisé

#### Licence
Ce projet est sous licence GNU Affero General Public License 3.0, voir [LICENSE](LICENSE) pour les conditions complètes. Selon cette licence :
1. Toute utilisation, modification ou distribution doit se conformer à cette licence
2. Lors de la fourniture de services via le réseau, le code source doit être mis à disposition
3. Toutes les modifications doivent être clairement marquées et conserver les avis de droit d'auteur originaux
4. Ne pas utiliser le nom du projet à des fins promotionnelles sans autorisation

## Pile technologique
- Framework backend : Express 4.x
- Moteur de template : EJS 3.x
- Base de données : MongoDB >=7.x
- Gestionnaire de paquets : npm 9.x

## Démarrage rapide
```bash
# Installer les dépendances
npm install

# Démarrer en mode développement (avec rechargement automatique)
npm run dev

# Démarrer en mode production
npm start

# Initialiser la base de données
node scripts/initDB.js
```

## Structure du projet (Architecture MVC)
```
├── app.js         # Point d'entrée de l'application
├── models/        # Modèles de données (M)
├── routes/        # Contrôleurs de route (C)
├── views/         # Templates de vue (V)
├── public/        # Ressources statiques
├── scripts/       # Scripts de migration de base de données
└── config/        # Configuration d'environnement
```

## Configuration de l'environnement
Créer un fichier .env :
```ini
PORT=3000
MONGODB_URI=mongodb://localhost:27017/cocokalo
SESSION_SECRET=your_session_secret
```

## Documentation API
Visitez `/api-docs` pour la documentation Swagger