# Règles à appliquer systématiquement

## Méthode

1. Pour chaque nouvelle fonctionnalité, créer une nouvelle branche en la tirant de la précedente et nommé avec le pattern feat/name-feature
2. Créer les tests fonctionnels avec cucumber afin de décrire fonctionnellement le besoin, l'implémentation de ces tests doivent être fait au fur et à mesure.
3. Ecrire les tests puis développer ensuite. Tout développement doit se faire en TDD (Test Driven Development) commencer par des tests unitaires qui échouent, puis développer jusqu'à au succès des tests.
4. A la fin du developpement, tous les tests (fonctionnels, unitaire et d'intégration) doivent être fini et fonctionnel ; L'application doit être fonctionnelle.
5. Coches la case correspodant à la fonctionnalité dans le fichier features.md à la racine du projet
6. Ajoutes au git les fichiers modifiés et créés et réalise un commit en respectant les règles du conventional commit (https://www.conventionalcommits.org/en/v1.0.0)
6. Demander à l'utilisateur si on passe à la nouvelle fonctionnalité

## Architecture

Le développement s'effectue en respectant scrupuleusement les règles de la clean architecture.

L'architecture s'organise comme suit et doit toujours être respectée

/src
L features.md
L README.md
L package.json
L LICENSE
L .gitignore
L .swcrc
L application
L __port__
L *.port.ts // interface à implémenter pour les répositories
L context
L *.use-case.ts
L *.request.ts
L *.response.ts
L *.presenter.ts // interface
L domain
L *.model.ts
L context
L *.model.ts
L entity
L *.entity.ts
L value-object
L *.value-object.ts
L infra
L nestjs
L main (dossier correspondant à tout ce qui est lié au bootstrap de l'application)
L main.ts
L main.module.ts
L main.service.ts
L presentation
L main.controller.ts
L *.dto.ts
L *.middleware.ts
L *.filter.ts
L *.guard.ts
L *.interceptor.ts
L *pipe.ts
L context (nom du contexte a adapté à chaque nouveau contexte, on parle ici de contexte au sens de la clean architecture)
L *.module.ts // Il doit y avoir un module pour les tests et un module pour le fonctionnement normal de l'application
L __repository__
L *.repository.ts // implémentation des ports
L presentation
L *.controller.ts
L *.presenter.ts // implémentation du présenteur de l'application
L *.dto.ts
L *.middleware.ts
L *.filter.ts
L *.guard.ts
L *.interceptor.ts
L *pipe.ts
L shared
L configuration
L *.config.ts
L constant
L *.constant.ts
L utils
L *.utils.ts


## Tests

Les tests sont toujours dans le dossier __test__, chaque contexte a un dossier __test__.
Les tests unitaires ont tous l'extension *.unit.ts et sont dans le dossier __test__/unit.
Les tests d'intégration ont tous l'extension *.int.ts et sont dans le dossier __test__/int.
Les tests e2e ont tous l'extension *.e2e.ts et sont dans le dossier __test__/e2e.
Les tests sont écrits avec jest.

Le fichier de configuration a utilisé pour les tests unitaires se nomme "jest-unit.config.ts" et contient la configuration suivante :

```ts
import type { Config } from "@jest/types";

const config: Config.InitialOptions = {
  preset: "ts-jest",
  testEnvironment: "node",
  transform: {
    // @ts-ignore
    "^.+\\.(t|j)s?$": ["@swc/jest"],
  },
  testMatch: ["**/__tests__/**/*.unit.ts", "**/__test__/**/*.unit.ts"],
  moduleDirectories: ["node_modules"],
  moduleNameMapper: {
    "^@domain/(.*)$": "<rootDir>/src/domain/$1",
    "^@application/(.*)$": "<rootDir>/src/application/$1",
    "^@infra/(.*)$": "<rootDir>/src/infra/$1",
    "^@shared/(.*)$": "<rootDir>/src/shared/$1",
  },
};

export default config;
```

Le fichier de configuration a utilisé pour les tests d'intégration se nomme "jest-int.config.ts" et contient la configuration suivante :

```ts
import type { Config } from "@jest/types";

const config: Config.InitialOptions = {
  preset: "ts-jest",
  testEnvironment: "node",
  transform: {
    // @ts-ignore
    "^.+\\.(t|j)s?$": ["@swc/jest"],
  },
  testMatch: [
    "**/__tests__/**/*.int.ts",
    "**/__test__/**/*.int.ts",
    "**/__tests__/**/*.integration.ts",
    "**/__test__/**/*.integration.ts",
  ],
  moduleDirectories: ["node_modules"],
  moduleNameMapper: {
    "^@domain/(.*)$": "<rootDir>/src/domain/$1",
    "^@application/(.*)$": "<rootDir>/src/application/$1",
    "^@infra/(.*)$": "<rootDir>/src/infra/$1",
    "^@shared/(.*)$": "<rootDir>/src/shared/$1",
  },
};
export default config;
```

## Tests fonctionnels

à la racine, il doit y avoir le fichier "cucumber.js" qui contient :

```js
const common = [
  "./src/**/*.feature",
  "--require cucumber.setup.js",
  "--require-module ts-node/register",
  "--require ./src/**/*.step.ts",
  "--require-module",
  "ts-node/register/transpile-only",
  '--format-options \'{ "snippetInterface" :"async-await"}\'',
  "--format summary",
].join(" ");

module.exports = {
  default: common,
};
```

Et le fichier "cucumber.setup.js" :

```js
// eslint-disable-next-line @typescript-eslint/no-var-requires,no-undef
require("ts-node").register({
  transpileOnly: true,
  require: ["tsconfig-paths/register"],
  compilerOptions: {
    module: "commonjs",
    resolveJsonModule: true,
    baseUrl: ".",
    paths: {
      "@infra/*": ["src/infra/*"],
      "@application/*": ["src/application/*"],
      "@domain/*": ["src/domain/*"],
      "@shared/*": ["src/shared/*"],
    },
  },
});
```

Les fichiers de tests fonctionnels doivent être présents dans le dossier __test__,
et plus particulièrement, les fichiers respectent le pattern *.feature dans __test__/feature
et les fichiers d'implémentations respectent le pattern *.step.ts et sont dans __test__/step

## Imports

Dans les dossiers et sous dossier application et domain, aucun élément du framework ne doit être présent. Seuls des imports commençant par @application, @domain et @shared sont possibles.

Le fichier tsconfig doit avoir toutes ses clés ordonnancé par ordre alphabétique.

Le fichier tsconfig doit contenir :

```json
"paths": {
      "@application/*": ["src/application/*"],
      "@domain/*": ["src/domain/*"],
      "@infra/*": ["src/infra/*"],
      "@shared/*": ["src/shared/*"]
},
```

Dans les fichiers typescript, l'ordre de l'import doit être celui là (chaque bloc d'import doit être séparé par un retour à la ligne) :
- import des packages node natif
- import des packages externe sauf nestjs
- import des packages nestjs
- import des fichiers du dossier domain
- fichiers du dossier application
- import des fichiers du dossier shared
- import des fichiers du dossier infra

Une excemption pour le fichier main.ts qui doit commencer par ces lignes :

```ts
import * as dotenv from "dotenv";
// DON'T MOVE THIS LINE! dotenv.config() must be at the start of imports
dotenv.config();

## Use case

un fichier use case doit contenir un constructor auquel on passe les ports nécessaire pour l'injection de dépendance,
et un execute asynchrone auquel on passe une classe request et une interface presenter.

Le presenter contient l'attribut "response" et al méthode "viewModel", les deux sont de type response (la response change selon le contexte et sont nom aussi bien sûr). Exemple : 

```ts
export interface TransformPpgXmlToJsonPresenter {
  response: TransformPpgXmlToJsonResponse;
  viewModel(): TransformPpgXmlToJsonResponse;
}
```

Exemple de réponse :

```ts
export default class TransformPpgXmlToJsonResponse {
  orderId: OrderId[];

  error: ErrorResponse;
}

```

## Package manager

Utiliser le package manager pnpm


## Lintage

L'application utilise biome pour linter et formater les fichiers

## Main

La route GET par défaut doit retourner un objet json avec la version de l'application (qui correspond à la version du package.json) et service (correspond au name du package.json où la première lettre est en majuscule et les tirets sont des espaces).

Exemple de class MainService :

```ts
@Injectable()
export default class MainService {
  getDefault(): DefaultResponse {
    return {
      version: packageContent.version,
      service: formatPackageName(packageContent.name),
    };
  }
}
```

## SWC

Le fichier ".swcrc" contient :

```json
{
  "$schema": "https://json.schemastore.org/swcrc",
  "sourceMaps": true,
  "jsc": {
    "parser": {
      "syntax": "typescript",
      "decorators": true,
      "dynamicImport": true
    },
    "transform": {
      "legacyDecorator": true,
      "decoratorMetadata": true
    },
    "baseUrl": "./"
  },
  "minify": false
}
```

## Features

Le fichier "features.md" se structure de la façon suivante :

```md
[x] fonctionnalité 1 
[ ] fonctionnalité 2
  - [ ] sous fonctionnalité 1 de la fonctionnalité 2
  - [ ] sous fonctionnalité 2 de la fonctionnalité 2
[ ] fonctionnalité 3
```
[x] correspond à une fonctionnalité réalisée
[ ] correspond à une fonctionnalité à réaliser

Réaliser les fonctionnalités dans l'ordre.
