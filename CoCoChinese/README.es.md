# Plataforma Anime Cocokalo

![Logo Cocokalo](public/images/project-logo.png)

#### Descripción
Una plataforma de video anime de código abierto con diseño de interfaz inspirado en Bilibili. Solo para fines de aprendizaje e investigación.

#### Aviso de derechos de autor
1. Este es un proyecto de aprendizaje de código abierto sin fines comerciales
2. Prohibido estrictamente usar Cocokalo para actividades no académicas
3. Todo el contenido anime utilizado en este proyecto pertenece a sus creadores originales
4. No utilice este proyecto para almacenar o distribuir contenido con derechos de autor no autorizado

#### Licencia
Este proyecto está licenciado bajo GNU Affero General Public License 3.0, consulte [LICENSE](LICENSE) para los términos completos. Bajo esta licencia:
1. Cualquier uso, modificación o distribución debe cumplir con esta licencia
2. Al proporcionar servicios a través de la red, debe poner el código fuente a disposición
3. Todas las modificaciones deben estar claramente marcadas y conservar los avisos de derechos de autor originales
4. No utilice el nombre del proyecto para promoción sin permiso

## Tecnologías
- Framework backend: Express 4.x
- Motor de plantillas: EJS 3.x
- Base de datos: MongoDB >=7.x
- Gestor de paquetes: npm 9.x

## Inicio rápido
```bash
# Instalar dependencias
npm install

# Iniciar en modo desarrollo (con recarga en caliente)
npm run dev

# Iniciar en modo producción
npm start

# Inicializar base de datos
node scripts/initDB.js
```

## Estructura del proyecto (Arquitectura MVC)
```
├── app.js         # Entrada de la aplicación
├── models/        # Modelos de datos (M)
├── routes/        # Controladores de ruta (C)
├── views/         # Plantillas de vista (V)
├── public/        # Recursos estáticos
├── scripts/       # Scripts de migración de base de datos
└── config/        # Configuración de entorno
```

## Configuración del entorno
Cree archivo .env:
```ini
PORT=3000
MONGODB_URI=mongodb://localhost:27017/cocokalo
SESSION_SECRET=your_session_secret
```

## Documentación API
Visite `/api-docs` para la documentación Swagger