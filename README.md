# Clean Code — Intermediate (Orta Seviye)

---

Programların basit görünmesini sağlayan dil değildir.

Dili basit gösteren programcıdır!

— Robert C. Martin (Uncle Bob)

---

**(KISS)** Keep It Simple, Stupid — Basit Tut, Aptal

**(DRY)** Don’t Repeat Yourself — Kendini Tekrar Etme

**(YAGNI)** You Aren't Gonna Need It — Buna İhtiyacın Olmayacak

---

<br/>

Functions — Fonksiyonlar

```tsx
// Basic function
function isAdmin(user) {
  return user.role === "admin";
}

// Arrow function
const isAdmin = (user) => {
  return user.role === "admin";
};

// Fonksiyon tek bir ifade içeriyorsa tek satır haline getirebiliriz.
const isAdmin = (user) => user.role === "admin";

// Bir başka örnek

const groceries = ["😀", "😃", "😆"];

groceries.forEach(function (item) {
  console.log(item); // Output: '😀','😃','😆'
});

// Tek bir ifade olduğu için tek satır haline getirelim.
groceries.forEach((item) => console.log(item));

// forEach bir geri arama (callback) fonksiyonu aldığı ve
// daha iyi okunabilirlik için ayrı satırlara çıkartabiliriz.
const groceries = ["😀", "😃", "😆"];
const logItem = (item) => console.log(item);
groceries.forEach(logItem);
```

<br/>

Guard clause — Koruma tümcesi

```tsx
// Koruma tümcesi genellikle bir fonksiyonun
// başlangıcında bulunan koşullu mantıktır.
// Belirli bir koşul karşılanırsa fonksiyondan erken döner.

// Traditional - Geleneksel

const messageShouldBeLogged = true

const logMessage = () => {   ❌
  if (messageShouldBeLogged) {
    const x = 1
    const y = 2
    console.log(`${x} plus ${y} is ${x + y} .`)
    // Output: 1 plus 2 is 3.
  }
}

// Guard clause — Koruma tümcesi ile

const logMessage = () => {   ✅
  if (!messageShouldBeLogged) return
    const x = 1
    const y = 2
    console.log(`${x} plus ${y} is ${x + y} .`)
    // Output: 1 plus 2 is 3.
}

// Nested logic - İç içe mantık

// Traditional - Geleneksel

const checkAuthentication = () => {   ❌
  if (userIsAuthenticated) {
    if (userIsAdmin) {
      showAdminDashboard()
    } else {
      showHomePage()
    }
  } else {
    redirectToLoginPage()
  }
}

// Guard clause — Koruma tümcesi ile

const checkAuthentication = () => {   ✅
  if (!userIsAuthenticated) return redirectToLoginPage()
  if (userIsAdmin) return showAdminDashboard()
  showHomePage()
}
```

<br/>

If statement — If ifadesi

```tsx
const getStageOfLifeByAge = (age) => {    ❌
  let stageOfLife = ''
  if (age < 1) {
    stageOfLife = 'Infancy'
  } else if (age >= 1 && age < 4) {
    stageOfLife = 'Toddlerhood'
  } else if (age >= 4 && age < 10) {
    stageOfLife = 'Childhood'
  } else if (age >= 10 && age < 21) {
    stageOfLife = 'Adolescence'
  } else if (age >= 21) {
    stageOfLife = 'Adulthood'
  }
  return stageOfLife
}

const age = 18
const description = getStageOfLifeByAge(age)
console.log(description) // Output: Adolescence

// Gereksiz mantığı kaldırın

const getStageOfLifeByAge = (age) => {   ✅
  if (age < 1) return 'Infancy'
  if (age < 4) return 'Toddlerhood'
  if (age < 10) return 'Childhood'
  if (age < 21) return 'Adolescence'
  if (age >= 21) return 'Adulthood'
  return stageOfLife
}

const age = 18
const description = getStageOfLifeByAge(age)
console.log(description) // Output: Adolescence

// Eşitsizlik (!==) operatöründen kaçının

const userIsAdmin = (user) => {    ❌
  if (user.role !== 'admin') return false
  if (user.manager !== true) return false
  return true
}

const userIsAdmin = (user) => {    ✅
  if (user.role === 'admin') return true
  if (user.manager === true) return true
  return false
}

// Kod ayıklamaları (extractions)    ✅

const userHasAdminRole = (user) => user.role === 'admin'
const userIsManager = (user) => user.manager
const userIsAdmin = (user) => userHasAdminRole(user) || userIsManager(user)
```

<br/>

Logical operators — Mantıksal operatörler

```tsx
// Mantıksal AND (&&) operatörü

const isAdmin = true;
const isAuthorized = true;

isAuthorized && isAdmin && console.log("Welcome, Admin!");

// Mantıksal OR (||) operatörü

const isAuthorized = false;

isAuthorized || console.log("User is not authorized");

// Bir başka örnek

const taxRate = undefined;
const netPrice = 100;
const salesTax = netPrice * (taxRate || 0.08); // (||) ile varsayılan değer
const grossPrice = netPrice + salesTax;
console.log(grossPrice); // Output: 108
```

<br/>

Logical assignment operators — Mantıksal atama operatörleri

```tsx
// Mantıksal AND (&&) atama operatörü

const user = {
  firstName: "Taner",
  lastName: "Çeker",
  credit: 46.34,
};

// user.credit varsa user.credit'e true değerini ata.
user.credit &&= true;

// Mantıksal nullish (??) atama operatörü

const user = {
  firstName: "Taner",
  lastName: "Çeker",
};

// user.taxRate boş (null) veya tanımsız (undefined) ise 0.34 değerini ata.
user.taxRate ??= 0.34;

// Mantıksal OR (||) atama operatörü

const book = {
  title: "JavaScript: 7th Edition",
  author: "",
  isbn: "98546347437421",
};

// book.author yoksa ilgili değeri ata.
book.author ||= "The author is missing.";
```

<br/>

Optional Chaining (?) — İsteğe Bağlı Zincirleme

```tsx
const user = {
  age: 27
}

const middleName = user.name.middle     ❌
// 'TypeError: Cannot read properties of undefined'

// İsteğe bağlı zincirleme operatörü ile kodunuzu
// istisnalardan (exceptions) koruyun.

// İç içe geçmiş özelliklere erişmek

const user = {
  age: 27
}

// soru (?) işareti ile bir önceki değişkenin var olup
// olmadığını kontrol ediyoruz.
// name değişkeni olmadığı için middle değişkenine geçmeyecektir.
// Tanımsız (Undefined) atayacaktır.
// Böylelikle istisnalardan korunmuş oluyoruz.

const middleName = user.name?.middle   ✅
```

<br/>

Template literals — Şablon değişmezleri

```tsx
const firstName = 'Taner'
const lastName = 'Çeker'
const greeting = 'Hi, ' + firstName + ' ' + lastName + '!'   ❌
console.log(greeting) // Output: Hi, Taner Çeker!

// Template literals `${}`

const greeting = `Hi, ${firstName} ${lastName}!`   ✅
console.log(greeting) // Output: Hi, Taner Çeker!

const birthYear = 2004
const legalAge = 21
const notice = `Your legal age for alcohol: ${birthYear + legalAge}`

const getBeverageByAge = (age) => {
  if (age >= 21) return '🍺'
  return '🥛'
}

const notice = `You are drinking ${getBeverageByAge(18)}.`
console.log(notice) // Output: You are drinking 🥛.

// Çok satırlı dizeler (strings)

const book = 'JavaScript: The Definitive Guide, 7th Edition' + '\n'   ❌
+ 'David Flanagan' + '\n' + '473743743843'

// `` kullanın
const book = `JavaScript: The Definitive Guide, 7th Edition   ✅
David Flanagan
473743743843`
```

<br/>

Ternary operator — Üçlü operatör

```tsx
// Koşullu değişken atama

const hourOfDay = new Date().getHours()
let partOfDay
if (hourOfDay < 17) {    ❌
  partOfDay = 'day'
} else {
  partOfDay = 'evening'
}

const hourOfDay = new Date().getHours()
let partOfDay = hourOfDay < 17 ? 'day' : 'evening'    ✅


// Koşullu zincirler

const hourOfDay = new Date().getHours()
let partOfDay
if (hourOfDay < 12) {    ❌
  partOfDay = 'morning'
} else if (hourOfDay < 17) {
  partOfDay = 'afternoon'
} else if (hourOfDay < 21) {
  partOfDay = 'evening'
} else {
  partOfDay = 'night'
}


const hourOfDay = new Date().getHours()
let partOfDay = 'night'
if (hourOfDay < 12) partOfDay = 'morning'    ❌
if (hourOfDay < 17) partOfDay = 'afternoon'
if (hourOfDay < 21) partOfDay = 'evening'


const hourOfday = new Date().getHours()
let partOfDay
switch (true) {    ❌
  case (hourOfDay < 12):
    partOfDay = 'morning'
    break;
  case (hourOfDay < 17):
    partOfDay = 'afternoon'
    break;
  case (hourOfDay < 21):
    partOfDay = 'evening'
    break;
  default:
    partOfDay = 'night'
}


const hourOfDay = new Date().getHours()

const getPartOfDayByHourOfDay = (hourOfDay) => {
  switch (true) {    ❌
    case (hourOfDay < 12):
      return 'morning'
    case (hourOfDay < 17):
      return 'afternoon'
    case (hourOfDay < 21):
      return 'evening'
    default:
      return 'night'
  }
}
const partOfDay = getPartOfDayByHourOfDay(hourOfDay)

const hourOfDay = new Date().getHours()
const partOfDay =    ✅
  hourOfDay < 12 ? 'morning' :
  hourOfDay < 17 ? 'afternoon' :
  hourOfDay < 21 ? 'evening' :
  'night'


// Koşullu şablon değişmezi

const isAuthenticated = false
isAuthenticated ? showHomepage() : redirectToLoginPage()


// Çoklu Koşullu şablon değişmezi

const isAuthenticated = false
const isUser = true
isAuthenticated ? showHomepage() :
isUser ? redirectToLoginPage() :
redirectToSignupPage()
```

<br/>

for loops — for döngüsü

```tsx
// for döngüsünün for...of ve forEach() ile değiştirilmesi

const users = [
  {
    firstName: "Jane",
    lastName: "Doe",
  },
  {
    firstName: "Richard",
    lastName: "Roe",
  },
];

// for
for (let i = 0; i < users.length; i++) {
  const fullName = `${users[i].firstName} ${users[i].lastName}`;
  console.log(fullName); // Output: Jane Doe, Richard Roe
}

// for...of — daha anlaşılır
for (const user of users) {
  const fullName = `${user.firstName} ${user.lastName}`;
  console.log(fullName); // Output: Jane Doe, Richard Roe
}

const world = "Hello";

// for
for (let i = 0; i < word.length; i++) {
  console.log(word[i]);
}
// Output: H, e, l, l, o

// for...of
for (const letter of word) console.log(letter);
// Output: H, e, l, l, o

// forEach
// forEach() yöntemi parametre olarak bir
// geri arama (callback) fonksiyonu alır.

const fruits = ["🍏", "🍌", "🍍"];
fruits.forEach((fruit) => console.log(`${fruit} becomes 💩`));

// Kod ayıklamaları (extractions)

const fruits = ["🍏", "🍌", "🍍"];
const digest = (fruit) => console.log(`${fruit} becomes 💩`);
fruits.forEach(digest);
```

<br/>

break & continue

```tsx
// Kontrol bayrağı (Control flag), kodun yürütülüp yürütülmeyeceğini
// belirleyen bir boolean'dır.

// aşağıdaki durum ilgili veri bulunsa dahi tüm döngüyü tamamlayacaktır
// buda performans sorunu demektir
const users = ['Jane Doe', 'Richard Roe']
let found = false
for (const user of users) {    ❌
  if (!found) {
    if (user === 'Jane Doe') {
      console.log(`We found ${user}!`)
      found = true
    }
  }
}

// break
// break anahtar sözcüğü döngüden erken çıkmanızı sağlar
const users = ['Jane Doe', 'Richard Roe']
for (const user of users) {    ✅
  if (user === 'Jane Doe') {
    console.log(`We found ${user}!`)
    break
  }
}

// continue
// continue anahtar sözcüğü ilgili veriyi atlamayı sağlar
const users = ['Jane Doe', 'Richard Roe']
for (const user of users) {    ✅
  if (user !== 'Jane Doe') continue
  console.log(`We found ${user}!`)
}

// break ve continue kodunuzun performansını artırmanızı sağlar
```

<br/>

Spread & Rest syntax — Spread ve Rest sözdizimi

```tsx
// Nesne klonlama (Object cloning)

// Object.assign() işlevinin yüzeysel bir kopya (shallow copy)
// oluşturduğunu unutmayın.

// Yüzeysel kopyanın (Shallow copy) anlamı
// kopyalanan nesnenin iç içe geçmiş özelliklerinin kaynak nesneninkilerle
// aynı referansı paylaşmasıdır, dolayısıyla kaynak nesnenin birinci düzey
// özelliklerini değiştirirseniz kopyalanan nesne aynı kalacaktır.
// ancak bazı iç içe geçmiş özellikleri değiştirirseniz
// kopyalanan nesne de değişecektir.

let user = {
  names: {
    first: 'Jane',
    last: 'Doe',
  }
}

let userCopy = Object.assign({}, user)

user.names = { first: 'John', last: 'Roe' }
console.log(userCopy)
// Kopyalanan nesne hala { user: { names: { first: 'Jane', last: 'Doe }}}

user.names.first = 'John'
console.log(userCopy)
// Kopyalanan nesne şimdi { user: { names: { first: 'John', last: 'Doe }}}


// JSON.stringify() ve JSON.parse() birlikte kullanıldığında bir nesnenin
// derin bir kopyasını (deep copy) oluşturur.
// bu yaklaşım nesneyi bir dizeye (string) dönüştürür ve dize (string)
// bir nesneye geri ayrıştırıldığında tüm olası referansları kaybolur.
// aşağıdaki derin kopya (deep copy) hiçbir şeye referans noktası olmayan
// tamamen yeni bir nesnedir.

// JSON.stringify(myObject) > nesneyi dizeye (string) çevirir.

// JSON.parse(myString) > dizeyi (string) nesneye (object) çevirir.

let userCopy = JSON.parse(JSON.stringify(user))


// spread (...variable)
// nesnenin yüzeysel kopyasını (shallow copy) oluşturur ve
// özelliklerini sıralamanızı sağlar

let userCopy = { ...user }
console.log(userCopy) // { user: { names: { first: 'Jane', last: 'Doe }}}


// structuredClone() - YENİ
// bu yöntem nesnenin derin bir kopyasını (deep copy) oluşturacaktır ve
// kaynak nesne değiştirilirse kopya nesne değişmeyecek aynı kalacaktır

let userCopy = structuredClone(user)


// Nesneleri birleştirme (Merging objects)

const userStatus = {
  active: true,
  admin: false
}

const userName = {
  firstName: 'Joe',
  lastName: 'Doe'
}

const user = {    ❌
  firstName: userName.firstName,
  lastName: userName.lastName,
  admin: userStatus.admin,
  active: userStatus.active
}

const user = Object.assign(userName, userStatus)    ✅

// spread ile
const user = { ...userName, ...userStatus }    ✅


// Dizileri birleştirmek (Concatenating arrays)

const fruits = ['🍏', '🍍']
const vegetables = ['🥒', '🥕']
const healthyFood = fruits.concat(vegetables) // '🍏', '🍍', '🥒', '🥕'

// spread ile
const healthyFood = [...fruits, ...vegetables] // '🍏', '🍍', '🥒', '🥕'


// Dizeyi (String) karakter dizisine (character array) dönüştürme

const word = 'Hello'

// spread ile
const letters = [...word]
console.log(letters) // ['H', 'e', 'l', 'l', 'o']


// Koşullu nesne özellikleri (Conditional object properties)

// koşullu olarak nesneye özellik eklemek

const middleName = 'Jennifer'

let user = {
  firstName: 'Jane',
  lastName: 'Doe',
  ...(middleName) && { middleName }
}


// Rest (...) sözdizimiyle bir fonksiyona parametre geçirme

const numbers = [1, 2, 3]
const logNumbers =    ❌
(x, y, z) => console.log(x) || console.log(y) || console.log(z)

logNumbers(numbers[0], numbers[1], numbers[2])    ❌ // 1, 2, 3

logNumbers(...numbers)     ✅ // 1, 2, 3

const logNumber = (number) => console.log(number)
const logNumbers = (...numbers) => numbers.forEach(logNumber)     ✅
logNumbers(1, 2, 3, 4, 5) // 1, 2, 3, 4, 5
```

<br/>

Object destructuring — Nesne yıkımı

```tsx
// Object destructuring
// const OR let { firstName, lastName, vs... } = myObject

// Nesne özelliklerine erişme

const user = {
  firstName: 'Jane',
  lastName: 'Doe',
}

const firstName = user.firstname || user[firstName] // aynı şeyler    ❌

const { firstName } = user     ✅

const { firstName, lastName } = user     ✅


// Yeni bir değişken oluşturma

const { firstName, lastName, fullName = `${firstName} ${lastName}` } = user   ✅

// ilk olarak nesnenin özelliklerini çıkartmamız gerekiyor.
// dolayısıyla aşağıdaki durum hata verecektir.
const { fullName = `${firstName} ${lastName}` } = user    ❌


// İç içe özellikler (Nested properties)

const response = {
  status: 'success',
  data: {
    firstName: 'Jane',
    lastName: 'Doe'
  }
}

const { status, data: { firstName }} = response     ✅


// Varsayılan değer (Default value)

const { status } = response
const { middleName } = response.data || 'Jennifer'

const { status, data: { middleName = 'Jennifer' }} = response


// Takma Adlar (Aliases)

// "data: user" nesnedeki data'yı user değişkeni olarak çıkarır
const { data: user, status } = response
console.log(user)

// "status: situation" nesnedeki status'u situation değişkeni olarak çıkarır
const { data: user, status: situation } = response
console.log(taner)


// Dizilerde rest özelliği (Rest property in Arrays)

// İlk öğeden kurtulmak
const fruits = ['🍏', '🥕', '🍍']
fruits.shift()    ❌
console.log(fruits) // ['🥕', '🍍']

// Yukarıdaki yaklaşım diziyi sonsuza kadar değiştirmiş olur.

// Aşağıdaki yaklaşım ilk diziyi olduğu gibi tutarken ilki dışındaki
// diğer tüm dizi öğelerinin yeni bir değişkene aktarır.

const fruits = ['🍏', '🥕', '🍍']
const [firstFruit, ...otherFruits] = fruits     ✅
console.log(firstFruit) // ['🍏']
console.log(otherFruits) // ['🥕', '🍍']


// İlk öğeyi hiç istemiyorsak eğer,

const fruits = ['🍏', '🥕', '🍍']
const [, ...otherFruits] = fruits     ✅
console.log(otherFruits) // ['🥕', '🍍']


// Fonksiyon parametre yıkımı (Destructuring function parameter)

const user = { name: 'Jane' }
function greetUser (user) {    ❌
  console.log(`Hello, ${user.name}!`)
}
greetUser(user) // Hello, Jane!

const user = { name: 'Jane' }
function greetUser ({ name }) {     ✅
  console.log(`Hello, ${name}!`)
}
greetUser(user) // Hello, Jane!


// Tek satırlık bir ifade olduğu için tek satır haline dönüştürelim

const user = { name: 'Jane' }
const greetUser = ({ name }) => console.log(`Hello, ${name}!`)     ✅
greetUser(user) // Hello, Jane!
```

<br/>

Array.map() & Array.filter()

```tsx
// Diziden (Array) koşullu olarak öğe silme

const animals = ['horse', 'dog', 'cat', 'rabbit', 'lynx']

let filteredAnimals = []

for(let i = 0; i < animals.length; i++){    ❌
  if(animals[i].includes('a')) {
    filteredAnimals.push(animals[i])
  }
}
console.log(filteredAnimals) // ['cat', 'rabbit']

for(const animal of animals){     ✅
  if(animal.includes('a')) {
    filteredAnimals.push(animal)
  }
}
console.log(filteredAnimals) // ['cat', 'rabbit']


// Tek satırlık hale AND (&&) operatörü ile getirebiliriz.

for(const animal of animals) animal.includes('a') && filteredAnimals.push(animal)     ✅
console.log(filteredAnimals) // ['cat', 'rabbit']


// Array.filter()
// Daha temiz yolu: filter yöntemini kullanmak
// Array.filter(), bir dizi (array) üzerinde iterasyon yaparak
// yalnızca istenen öğeleri bırakan bir yöntemdir.
// filter() gövdesi içinde geri arama (callback) fonksiyonu yürütür

const animals = ['horse', 'dog', 'cat', 'rabbit', 'lynx']

const filteredAnimals = animals.filter(animal => animal.includes('a'))   ✅

console.log(filteredAnimals) // ['cat', 'rabbit']


// geri çağırma fonksiyonunu (callback function) ayıklayabiliriz

const animals = ['horse', 'dog', 'cat', 'rabbit', 'lynx']

const hasLetterA = (word) => word.includes('a')
const filteredAnimals = animals.filter(hasLetterA)     ✅

console.log(filteredAnimals) // ['cat', 'rabbit']


// Array.map()
// bir dizi kullanıcımız var ve onların göbek adlarını ayrı bir
// diziye çıkarmak istiyoruz.

const users = [
  {
    firstName: 'Jane'
    middleName: 'Jennifer'
    lastName: 'Doe'
  },
  {
    firstName: 'Richard'
    lastName: 'Roe'
  },
  {
    firstName: 'John'
    middleName: 'Jack'
    lastName: 'Joe'
  },
  {
    firstName: 'Gina'
    middleName: 'Jennifer'
    lastName: 'Woe'
  }
]

let middleNames = []

for (let i = 0; i < users.length; i++) {    ❌
  middleNames.push(users[i].middleName)
}
console.log(middleNames) // ['Jennifer', undefined, 'Jack', 'Jennifer']

const middleNames = users.map(({ middleName }) => middleName)     ✅
console.log(middleNames) // ['Jennifer', undefined, 'Jack', 'Jennifer']


// var olmayan göbek adları (undefined) için filter yöntemini kullanabiliriz

const middleNames = users.map(({ middleName }) => middleName)
const middleNamesThatExist = middleNames.filter(middleName => middleName) ✅
console.log(middleNamesThatExist) // ['Jennifer', 'Jack', 'Jennifer']


// zincirleme yaparak tek satır hale getirebiliriz

const middleNamesThatExist =
users.map(({ middleName }) => middleName).filter(middleName => middleName) ✅
console.log(middleNamesThatExist) // ['Jennifer', 'Jack', 'Jennifer']


// geri arama (callback) fonksiyonlarını ayıklayabiliriz

const extractMiddleName = ({ middleName }) => middleName
const isTruthy = (item) => item
const middleNamesThatExist = users.map(extractMiddleName).filter(isTruthy) ✅
console.log(middleNamesThatExist)// ['Jennifer', 'Jack', 'Jennifer']


// burada aslında "isTruthy" geri arama fonksiyonuna ihtiyacımız yok.
// onun yerine filter yöntemine Boolean geçebiliriz.
// böylelikle filter veri varsa true yoksa false döndürecektir

const extractMiddleName = ({ middleName }) => middleName
const middleNamesThatExist = users.map(extractMiddleName).filter(Boolean) ✅
console.log(middleNamesThatExist)// ['Jennifer', 'Jack', 'Jennifer']


// Tekrarlanan değerleri kaldırma

const extractMiddleName = ({ middleName }) => middleName
const middleNamesThatExist = users.map(extractMiddleName).filter(Boolean)

let uniqueMiddleNames = []
for (const middleName of middleNamesThatExist) {   ❌
  if (!uniqueMiddlenames.includes(middleName)) {
    uniqueMiddleNames.push(middleName)
  }
}
console.log(uniqueMiddleNames) // ['Jennifer', 'Jack']


for (const middleName of middleNamesThatExist) {
  uniqueMiddlenames.includes(middleName) || uniqueMiddleNames.push(middleName)  ✅
}
console.log(uniqueMiddleNames) // ['Jennifer', 'Jack']


// Veya makul boyutta dizilerle çalışıyorsanız çift filter'li yöntemi
// kullanabilirsiniz. yüksek boyutlu dizilerde performans zorlayıcı olabilir.

const extractMiddleName = ({ middleName }) => middleName
const isNotPreviousItem = (item, index, array) => array[index - 1] !== item
const middleNamesThatExist = users
			 .map(extractMiddleName)
			 .sort() // az dan çok a sıralıyor
			 .filter(Boolean) // yok ise kaldırıyor
			 .filter(isNotPreviousItem) // aynı ise kaldırıyor
console.log(uniqueMiddleNames) // ['Jennifer', 'Jack']
```

<br/>

Array.reduce()

```tsx
// Dizi öğelerini toplama (Summing array items)
// reduce() dizi öğelerinde akümülasyon yaparak öğeleri toplar
// ve yine reduce() gövdesine bir geri arama (callback) fonksiyonu alır

const numbers = [1, 5, 4, 2, 9]

let result = 0
for (let i = 0; i < numbers.length; i++) {    ❌
  result += numbers[i]
}
console.log(result) // 21

const sum = (a, b) => a + b
const result = numbers.reduce(sum, 0) // '0' başlangıç değeridir    ✅
console.log(result) // 21


// Dizi öğelerini sayma (Counting array items)

const animals = ['horse', 'dog', 'cat', 'rabbit' 'lynx']
const addIfHasLetterA = (accumulator, word) => accumulator + word.includes('a')  ✅
const animalsWithA = animals.reduce(addIfHasLetterA, 0)
console.log(animalsWithA) // 2


// Çoklu oluşumları sayma (Counting multiple occurrences)

const names = ['Jennifer', 'Jack', 'Jennifer']

let occurrences = {}
for (const name of names) {    ❌
  if (name in occurrences) {
    occurrences[name] = occurrences[name] + 1
  } else {
    occurrences[name] = 1
  }
}
console.log(occurrences) // { Jennifer: 2, Jack: 1 }

const addOccurrence =
(object, key) => ({ ...object, [key]: (object[key] || 0) + 1})
const occurrences = names.reduce(addOccurrence, {})      ✅
console.log(occurrences) // { Jennifer: 2, Jack: 1 }


// Tekrarların kaldırılması (Removing duplicates)

const names = ['Jennifer', 'Jack', 'Jennifer']

let uniqueNames = []

for (const name of names) {    ❌
  if (!uniqueName.includes(name)) {
    uniqueNames.push(name)
  }
}
console.log(uniqueNames) // ['Jennifer', 'Jack']

const isNotPreviousItem = (item, index, array) => array[index - 1] !== item
const uniqueNames = names.sort().filter(isNotPreviousItem)    ✅
console.log(uniqueNames) // ['Jennifer', 'Jack']


// Daha temiz yolu reduce() ile

const addToArrayIfUnique =
(array, item) => array.includes(item) ? array : [...array, item]
const uniqueNames = names.reduce(addToArrayIfUnique, [])    ✅
console.log(uniqueNames)// ['Jennifer', 'Jack']


// Ardışık Düzen - Boru Hattı (Pipeline)
// bir değer üzerinden bir dizi ardışık işlem yapmamız gerekir diyelim ki
// 3000 dolar geliriniz var ve gelir vergisi hesaplamak istiyorsunuz
// bunun için önce geliri hesaplamamız gerekiyor sonra
// 10'luk vergi indirimini çıkarmamız ve ardından bu toplamın 20'sini
// hesaplamamız gerekiyor.

const calculateIncome = (revenue) => revenue / 2
const subtractDeduction = (income) => income / 100 * 90
const calculateTax = (taxBase) => taxBase / 100 * 20

const revenue = 3000

const income = calculateIncome(revenue)
const taxBase = subtractDeduction(income)
const tax = calculateTax(taxBase)
console.log(tax) // 180

// yukarıdaki kullanım tekrarlanan ve fonksiyonların miktarı
// çok daha büyük rutinlerde zorlayıcı olabilir.
// işte bu noktada reduce() kullanabiliriz.

const executeFunction = (accumulator, fn) => fn(accumulator)
const pipeline = [calculateIncome, subtractDeduction, calculateTax]
const tax = pipeline.reduce(executeFunction, revenue)    ✅
console.log(tax) // 180
```

<br/>

Array.some & Array.every

```tsx
const users = [
  {
    firstName: 'Jane'
    middleName: 'Jennifer'
    lastName: 'Doe'
  },
  {
    firstName: 'Richard'
    lastName: 'Roe'
  },
  {
    firstName: 'John'
    middleName: 'Jack'
    lastName: 'Joe'
  },
  {
    firstName: 'Gina'
    middleName: 'Jennifer'
    lastName: 'Woe'
  }
]

let areThereJennifers = false
for (const user of users) {    ❌
  if(user.middleName !== 'Jennifer') continue
  areThereJennifers = true
  break
}
console.log(areThereJennifers) // true

const userMiddleNameIsJennifer = ({ middleName}) => middleName === 'Jennifer'
const areThereJennifers = Boolean(users.filter(userMiddleNameIsJennifer).length)
console.log(areThereJennifers) // true


// Array.some() ile daha temiz yol:
// Array.some(), dizideki en az bir elemanın bir koşulu
// sağlayıp sağlamadığını kontrol eder

const userMiddleNameIsJennifer = ({ middleName}) => middleName === 'Jennifer'
const areThereJennifers = users.some(userMiddleNameIsJennifer)    ✅
console.log(areThereJennifers) // true


// genel bir fonksiyon haline getirelim

const userMiddleNameIs = (middleName) => (user) => user.middleName === middleName
const areThereJennifers = users.some(userMiddleNamesIs('Jennifer'))
console.log(areThereJennifers)// true


// Array.every()
// Array.every(), dizideki belirli tüm öğelerin belirli bir koşulu karşılayıp
// karşılamadığını kontrol etmemizi sağlar

const userMiddleNameIs = (middleName) => (user) => user.middleName === middleName
const isEveryoneJennifer = users.every(userMiddleNameIs('Jennifer'))    ✅
console.log(isEveryoneJennifer) // false


// dizi içerisindeki tüm değerlerin var olduğunu kontrol edebiliriz.

const values = [1, true, 'string']
const allTruthy = values.every(Boolean)
console.log(allTruthy)
```

<br/>

Asynchronous code — Eşzamansız kod

```tsx
const options = {
  method: 'POST',
  headers: { 'Content-Type: application/json' }
}

const organization = { name: 'Acme Inc.' }
const person = { firstName: 'Jane', lastName: 'Doe' }
const deal = { title: 'New deal' }

// callback hell
const createNewDeal = () => {    ❌
  fetch('/organizations', {
      body: JSON.stringify(organization),
      ...options
    })
    .then(response => response.json())
    .then({
      organizationId
    }) => {
      return fetch('/persons', {
          body: JSON.stringify({
            ...person,
            organizationId
          }),
          ...options
        })
        .then(response => response.json())
        .then(({
          personId
        }) => {
          return fetch('deals', {
              body: JSON.stringify({
                ...deal,
                personId
              }),
              ...options
            })
            .then(response => response.json())
            .then(({
              dealId
            }) => {
              console.log(dealId) // ID
            })
        })
    })
}


// async & await
// Async fonksiyonlar async anahtar sözcüğü ile bildirilir
// ve bir şeylerin olmasını beklemenizi (await) sağlar.

// await, promise tamamlanana, yani yerine getirilene (fulfilled) veya
// reddedilene (rejected) kadar yürütmeyi duraklatır.

const createNewDeaul = async () => {    ✅
  const organizationResponse = await fetch('/organizations', { body: JSON.stringify(organization), ...options })
  const { organizationId } = await organizationResponse.json()

  const personResponse = await fetch('/persons', { body: JSON.stringify({ ...person, organizationId }), ...options })
  const { personId } = await personResponse.json()

  const dealResponse = await fetch('deals', { body: JSON.stringify({ ...deal, personId }), ...options })
  const { dealId } = await dealResponse.json()

  console.log(deadId) // the deal ID
}


// Aynı anda birden fazla promise beklemek.
// (Awaiting multiple promises simultaneously)

const getUser = async () => {
  const userResponse = await fetch('/users/1')
  return userResponse.json();
}

const getBooks = async () => {
  const booksResponse = await fetch('/users/1/books')
  return booksResponse.json();
}

// sıralı istekler
// sıralı yaklaşımı kullanmanın kodun tamamlanması için
// gereken süreyi yaklaşık iki katına çıkarmasıdır

const compileData = async () => {
  const user = await getUser()
  const books = await getBooks()
  const compiledData = { ...user, books}
}

// sırasız istekler
// çeşitli kaynaklardan farklı veriler almak zorunda kalsaydık ve
// tek umursadığımız şey bunların bir arada gelmesi olsaydı
// ve verilerin sırasını umursamasaydık

const compileData = async () => {
  const [user, books] = await Promise.all([getUser(), getBooks()])
  const compiledData = { ...user, books}
}

// Promise ayrıca hem reddetmeleri (rejections) hem de
// yerine getirmeleri (fulfillments) işleyen allSettled() yöntemine sahiptir.
```

<br/>

20 Syntax shortcuts — 20 Sözdizimi kısayolları

```tsx
let x    ❌
let y
let z = 1


let x, y, z = 1    ✅


const firstName = 'Taner', lastName = 'Çeker'    ✅


// Bileşik ifadeler oluşturmak için virgül kullanın.

const { log } = console
const square = (number) => {    ❌
  log(`Squaring...`)
  return number  2
}
const result = square(4) // `Squaring...`
log(result) // 16

const { log } = console
const square = (number) => (log(`Squaring...`), number  2)    ✅
const result = square(4) // `Squaring...`
log(result) // 16


// Nesne özelliği kısaltması

const firstName = 'Jane', lastName = 'Doe'    ❌
const user = {
  firstName: firstName,
  lastName: lastName
}

const firstName = 'Jane', lastName = 'Doe'
const user = { firstName, lastName }    ✅


// Yüzer sayıları kesme (Truncating floats)
// Sayılardaki ondalık kısımları kaldırır

const number = 23.9
const bitwiseOr = number | 0    ✅
const bitwiseNot = ~~number    ✅
console.log(bitwiseOr) // 23
console.log(bitwiseNot) // 23


// Dizideki tekrarları kaldırma

const items = [1, 1, 2, 3, 5, 5, 1]
const uniqueItems = [...new Set(items)]
console.log(uniqueItems)


// 50/50 boolean
// %50 şans ile true veya false

const randomBoolean = Math.random() >= 0.5
console.log(randomBoolean)


// Alfabetik olarak sırala

const unsortedArray = ['Spread', 'rest', 'reduce', 'ternary', 'if', 'For', 'arrow']
const compareWords = (a, b) => a.localeCompare(b)
const sortedArray = unsortedArray.sort(compareWords)
console.log(sortedArray)
// ['arrow', 'For', 'if', 'reduce', 'rest', 'Spread', 'ternary']


// Varsayılan fonksiyon parametreleri

const greet = (name, message = 'Hello') => `${message}, ${name}!`
console.log(greet('Jane')) // Hello, Jane!
console.log(greet('Jane', 'Hola')) // Hola, Jane!


// Çoklu koşullar (Multiple conditions)

const food = 'apple'
const fruits = ['apple', 'banane', 'peach', 'pear', 'orange', 'pineapple']
fruits.includes(food) && console.log('It is a fruit!') // It is a fruit!
```

<br/>

Conversions — Dönüştürmeler

```tsx
// Sayıyı dizeye dönüştürme

const number = 5;
const string = String(number); // VEYA `${number}`
console.log(string); // `5`
console.log(typeof string)`string`;

// Dizeyi sayıya dönüştürme

const string = "5";
const number = Number(string);
console.log(number); // 5
console.log(typeof number)`number`;

// Diziyi dizeye dönüştürme

const numbers = [1, 2, 3, 4, 5];
const strings = numbers.map(String);
console.log(strings); // ['1', '2', '3', '4', '5']

// Dizeleri sayıya dönüştürme

const strings = ["1", "2", "3", "4", "5"];
const numbers = strings.map(Number);
console.log(numbers); // [1, 2, 3, 4, 5]

// Öğeleri boolean'lara dönüştürme

const values = ["string", 0, 1, null, undefined, NaN];
const booleans = values.map(Boolean);
console.log(booleans); // [true, false, true, false, false, false]

// Boolean'lara dönüştürme

const number = 0;

// bir değeri basitçe Boolean'a dönüştürmek için
// iki ünlem işareti kullanabiliriz
const falsyBoolean = !!number;
console.log(falsyBoolean); // false
console.log(typeof falsyBoolean); // `boolean`

// bir değeri dolaylı olarak bir Boolean'a dönüştürmek ve onu çevirmek
// için tek bir ünlem işareti kullanabiliriz
const truthyBoolean = !number; // sayı değilse
console.log(truthyBoolean); // true
console.log(typeof truthyBoolean); // `boolean`
```
