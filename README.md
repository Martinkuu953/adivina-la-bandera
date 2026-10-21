# 🌎 Adivina la Bandera

Juego móvil hecho con **React Native + Expo SDK 57**: aparece una bandera y hay que adivinar el país
(o su capital). Usa la API de [countriesnow.space](https://countriesnow.space).

## Cómo correrlo en el celular (Expo Go)

1. Instalá **Expo Go** en el celular. Tiene que ser una versión compatible con **SDK 57**
   (en Android, si la de la tienda es más nueva, bajá la de SDK 57 desde https://expo.dev/go).
2. En la compu, dentro de esta carpeta:

   ```bash
   npm install
   npx expo start
   ```

3. Escaneá el código QR: en Android desde la app Expo Go, en iPhone con la cámara.
   - La compu y el celular tienen que estar en la **misma red Wi-Fi**.
   - Si no conecta (redes de facultad, firewall de Windows), usá el modo túnel:
     `npx expo start --tunnel`

## Reglas

- 10 banderas por partida, 15 segundos cada una.
- **Acierto:** +10 puntos + los segundos que sobren en el timer, y pasa a otro país al azar.
- **Error:** −1 punto (se puede seguir intentando con la misma bandera).
- Si se acaba el tiempo, se muestra la respuesta y pasa a la siguiente.
- **Pistas:** cada 5 s llega una sola; pedir una a mano descuenta 2 s del timer.
  - Modo Banderas: se revela una letra al azar del nombre.
  - Modo Capitales: se borra una opción incorrecta.

## Estructura

```
App.js                      GameProvider envuelve toda la app y elige la pantalla
src/context/GameContext.js  TODO el estado y la lógica del juego (createContext + useContext)
src/components/             Cada componente lee lo que necesita con useGame() (sin props)
  Flag, GuessForm, ScoreBoard, Timer, Leaderboard,
  HintDisplay, CapitalOptions, RoundBanner, GameActions, GameHeader, PlayerSetup
src/screens/                HomeScreen, GameScreen, GameOverScreen
src/storage/leaderboard.js  Ranking persistido en localStorage
src/utils/game.js           Funciones puras (normalizar texto, mezclar, opciones, URLs de banderas)
```

### Requisitos de la consigna

| Requisito | Dónde |
|---|---|
| `useEffect` al montar trae los países y elige uno al azar | `GameContext.js` (efecto con `fetchCountries`) |
| Países, país actual y puntaje en `state` | `useState` en `GameProvider` |
| Todo el estado en un `GameContext` + `GameProvider` | `src/context/GameContext.js` |
| Ningún dato del juego por props | Todos los componentes usan `useGame()` |
| Plus 1 — Timer de 15 s + bonus por segundos restantes | `Timer.js` + `correctAnswer()` en el context |
| Plus 2 — Multijugador con ranking persistido | `PlayerSetup.js`, `Leaderboard.js`, `storage/leaderboard.js` |
| Plus 3 — Pistas (letra al azar, −2 s al pedirla) | `HintDisplay.js`, `GameActions.js`, `requestHint()` |
| Plus 4 — Modo Capitales (3 opciones, borra una cada 5 s) | `CapitalOptions.js` + API `/countries/capital` |

### Notas técnicas

- **localStorage en el celular:** React Native no tiene `localStorage`. `expo-sqlite/localStorage/install`
  (incluido en Expo Go) instala un `localStorage` global con la misma API que el del navegador,
  y los datos quedan guardados en el teléfono.
- **Banderas:** la API devuelve SVGs de Wikimedia. Se pide primero el PNG que Wikimedia genera
  a partir de ese SVG, después el SVG original y, si Wikimedia no responde, `flagcdn.com` por código ISO.
- Los nombres de países vienen en inglés desde la API; mientras escribís aparecen sugerencias
  para tocar directamente.
