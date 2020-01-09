# Vue Swipeable Card

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

## Project variables
```
<SwipeablePoiCardDeck
      :cards="visibleCards"
      :width="180"
      :height="350"
      :margin="30"
      :offset="12"
      @cardAccepted="handleCardAccepted"
    />
width: Card Width
header: Card Height
offset: Card Height Offset
```