# Setup

#### Volta
- backed by linkedIn.
- ensure I have the right version of my stuff on my repo.
  - for node
  - for yarn

#### nullish
```typescript
const a = undefined;

// that work, because it's null
const b = a ?? "no element"

// I will have false, because false is not nullish value
const c = false ?? "no element"
```

#### typing tuple
Je peux dire ce qu'il y a dans un tableau start & end :
```typescript
type Foo<T extends any> = [boolean, ...T, boolean]
````

Je peux nomer les argument de mes arrays
```typescript
type Adresse = [
  streetNumber: number,
  city: string,
  state: string,
  postal: string
] 

function printAddress(...address: Adresse){}
// I get the name of the param :).
printAddress(122, "san")
```

#### Use ts-expect-error
It's better to use ts-expect-error at place of ts-ignore

#### Don't use try/catch
- Don't use it as control flow, it's only for exeptionnal stuff.


I can unknown catch error to force check them.
```typescript
import { Simulate } from 'react-dom/test-utils'; import error = Simulate.error; 
function doSomethingRisky() {}

// use assertion to test, to check it's the right data type
function assertIsError(err: any): asserts err is Error {
  if (!(err instanceof Error)) throw new Error(`Not an error ${err}`)
}
// is like
function isError(err: any): err is Error {
 return err instanceof Error
}

try {
doSomethingRisky()
} catch (err: unknown) {
  if (err instanceof Error) {
    console.log(err.stack)
  } else {
    // if not error I throw, so next line is an error !
    assertIsError(err)
    console.log(err)
  }
}
````
#### type import
Permet d'importer uniquement le type, c'est util pour les bundlers,
il n'importerons pas la logique pour le client.
```typescript
import type {superType} from '../professional-ts'
````

## Setup new project
- `npx gitignore node`
- package.json
- volta pin node yarn
- tsc --init

Dans le tsconfig.json, si je fais une librarie, ne pas activer :
- skipLibCheck
- esModuleInterop
Car si ces options sont activés, elles devronts l'etre pour tout ceux qui
utilise ma lib.

set `declaration: true`
set `stripInternal:true`, ceci permet du suprimer les test & function qu'on
va utiliser

Laisser le `types: []` vide, => les globaux types seront exporter avec la lib,
et le consurmer pourrai se retrouvera avec jest ou autre dans ces elements.

## Add eslint
- `tsconfig.eslint.json` pour ne pas ship les test avec tous le reste, donne
  une config de test qui extends celle de la lib, a utiliser en intern.

- Je peux avoir des fichiers tsconfig specifique en fonction de la ou je suis,
Par exemple ici, ce ts.config sera uniquement pour mes tests,
et il n'emit pas de file:
```json
{
  "extends": "../tsconfig.json",
  "references": [{
    "name": "my-lib",
    "path": ".."
  }],
  "compilerOptions": {
    "types": ["jest"],
    "rootDir": "..",
    "noEmit":true
  },
  "include": ["."]
}
````
- le repo pour la lib:
https://github.com/mike-north/professional-ts-my-lib

## API extractors:
- `yarn api-extractor init`
- in api-extractor file:
  - setup mainEntryPointFilePath
  - dtsRollup: true

I don't know if I will use that in the futur, but I know why it's cool !

## Local type overides
- je peux overwrite les types de typescript avec un dossier types que je mets
dans mon repo, typescript ira directement les chercher.
- je fais un types directory
- dans ts config:
```json
baseUrl: ".",
paths: {
  "*": ["types/*"]
}
```
- Je peux utiliser trace resolution pour voir tout ce que ts fait

## Comment check les types durant le runtimes:
```typescript
type MyType = {
  nb: number
  name: string
}

function runTimeGuard(val: MyType): val is MyType {
  return typeof val.nb === "number" 
  && typeof val.name === "string"
}


function assertIsTypedArray<T>(
  arg:any,
  check: (val: any) => val is T
) : asserts arg is T[]{
   if (!Array.isArray(arg)) 
     throw new Error(`Not an array: ${JSON.stringify(arg)}`)
  if (arg.some(runTimeGuard)) 
    throw new Error(`Violators found: ${JSON.stringify(arg)}`)
}
````



































