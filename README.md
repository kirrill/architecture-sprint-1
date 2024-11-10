# Задание 1.
## Уровень 1. 
В качестве фреймворка для микрофронтенда выбран Webpack Module Federation
т.к. он не сильно отличается по возможностям от Single SPA, но на мой взгляд проще в реализации. К тому же, приложение построено на одном JS-фреймворке - React, и поддержка разных фреймворков не требуется, поэтому в Single SPA нет необходимости.

## Уровень 2. 
### Анализ текущих компонентов и их зависимостей:
```
/components
   #card AddPlacePopup.js: [PopupWithForm.js] - 
   #host App.js:[...] - корень приложения, взаимодействие с дочерними компонентами идет напрямую через import
   #card Card.js: [CurrentUserContext.js] - обработка кликов по карточке (Like, Delete, View)
   #prof EditAvatarPopup.js: [PopupWithForm.js] - изменение аватара пользователя
   #prof EditProfilePopup.js: [PopupWithForm.js, CurrentUserContext.js] - 
   #host Footer.js - футер основной страницы
   #host Header.js - заголовок страницы + обрабатывает маршруты
   #card ImagePopup.js - просомотр изображения на карточке
   #auth InfoTooltip.js - popup об успешной/неуспешной регистрации
   #auth Login.js: [../blocks/login/login.css] - форма входа
   #host Main.js: [Card.js, CurrentUserContext.js] - отрисовка основной части страницы
   #all PopupWithForm.js - базовый шаблон формы popup
   #host ProtectedRoute.js - редиректит на /signin, если пользователь не залогинен
   #auth Register.js - форма регистрации
/context
   #all CurrentUserContext.js - хранит состояние пользователя
/utils
    #prof #card api.js - API по работе с пользователем и карточками
    #auth auth.js - API по аутентификации
```

### Определяем бизнес-функции:
```
#auth Аутентификация - регистрация и вход пользователя
#prof Профиль пользователя - изменение имени, описания и аватара пользователя
#card Работа с карточками - добавление, удаление, лайки
#host Каркас приложения - основная страница приложения, которая будет подгружать остальные микрофронтенды
#all  Общие компоненты - компоненты, которые используют несколько микрофронтендов. Решение по ним ниже

#all PopupWithForm.js - добавляем в каждый микрофронтенд, чтобы микрофронтенды не зависели друг от друга. (Можно конечно сделать его общей библиотекой, но наша цель максимальная изоляция)
#host CurrentUserContext.js - выносим в микрофронтенд host, т.к. это глобальное состояние пользователя, о котором должны знать все микрофронтенды
```

### Разбивка по микрофронтендам
```
/host-mf                     // Хост
 /src
    /components
      App.js                 //  
      Main.js                // 
      Header.js              // 
      Footer.js              // 
      ProtectedRoute.js      // 
    /context
      CurrentUserContext.js  // 
    /images
      logo.svg
    /styles
      header.css             //  
      footer.css             //  
      content.css            //      

    index.js                 // 
  package.json               // 
  webpack.config.js          // exposes {CurrentUserContext.js}, remotes {}

/auth-mf
  /src
    /components
      Login.js               // 
      Register.js            // 
      InfoTooltip.js         // 
    /images
      success-icon.svg
      error-icon.svg
    /styles
      login.css              // 
      register.css           // 
    /utils
      auth.js                // 
    index.js                 // 
  package.json               // 
  webpack.config.js          // exposes {Login, Register, InfoTooltip}

/prof-mf 
  /src
    /components
      EditAvatarPopup.js     // 
      EditProfilePopup.js    // 
      PopupWithForm.js       // 
    /styles
      profile.css
      popup.css
    /utils
      api.js                 // API: Оставить методы: getUserInfo, setUserInfo, setUserAvatar
    index.js                 // 
  package.json               // 
  webpack.config.js
/card-mf          
  /src
    /components
      AddPlacePopup.js       // 
      Card.js                // 
      ImagePopup.js
      PopupWithForm.js       // 
    /styles
      card.css
      popup.css
      places.css
    /utils
      api.js                 // API: Оставить методы: getCardList, addCard, removeCard, changeLikeCardStatus
    index.js                 // 
  package.json               // 
  webpack.config.js          // exposes {AddPlacePopup,Card,ImagePopup}
```

# Задание 2.
https://drive.google.com/file/d/1XdZewliAUjiUJ1FOPLdVR6WXQlLqkZdv/view?usp=sharing