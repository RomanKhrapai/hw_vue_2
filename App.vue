
<template>
    <div class="name-box">
        <div class="user">
            <Title v-show="isShowTitle" :textValue="'імя та прізвище'"> </Title>
            <p> <span class="user-text">{{ user.firstName }}</span> {{ " " }}
                <span class="user-text">{{ user.lastName }}</span>{{ " " }}
                <span class="user-text">{{ user.age + " років" }}</span>
            </p>
            <p>{{ user }}</p>
            <div class="box-border name-box">

                <form id="jsonForm" class="form-user box-border">
                    <label for="id">ID: {{ user.id }}</label>
                    <label for="firstName">Ім'я: <input type="text" id="firstName" name="firstName"
                            v-model="user.firstName"></label>
                    <label for="lastName">Прізвище: <input type="text" id="lastName" name="lastName"
                            v-model="user.lastName"></label>
                    <label for="age">Вік: <input type="number" id="age" name="age" v-model="user.age"></label>
                    <label for="city">Місто: <input type="text" id="city" name="city" v-model="user.address.city"></label>
                    <label for="index">Індекс: <input type="number" id="index" name="index"
                            v-model="user.address.index"></label>
                    <label for="children">Діти: <input type="checkbox" id="children" name="children"
                            v-model="user.children"></label>
                </form>

                <div class="box-border box-colunm">
                    <button id="btn-1" @click="createNewUser"> Новий користувач</button>
                    <button id="btn-1" @click="changeUserAge"> Додати рік користувачу</button>
                    <button id="btn-2" @click="changeUserAge"> Відняти рік користувачу</button>
                    <button @click="changeShowTitle"> Змінити видимість заголовку</button>
                    <button @click="addUsers">Запит за пунктами в списку</button>
                </div>
            </div>

            <ul class="list-wrap">
                <li class="box-border" v-for="user in combineUsers" :key="user.id">
                    <span class="bold">ID:</span>{{ user.id }}<br>
                    <span class="bold">Ім'я:</span> {{ user.firstName }}<br>
                    <span class="bold">Прізвище:</span> {{ user.lastName }}<br>
                    <span class="bold">Вік:</span>{{ user.age }}<br>
                    <span class="bold">Адреса:</span> {{ user.address?.city }}<br>
                    <span class="bold">Індекс:</span>{{ user.address?.index }}<br>
                    <span class="bold">Діти:</span> {{ user.children ? "так" : "ні" }}
                </li>
            </ul>
        </div>
        <div class="users">
            <h3>Введіть країну</h3>
            <input class="input-country" type="text" v-model="country">
            <ul>
                <li v-for="item in countryList.slice(0, 5) " :key="user.id">
                    {{ item + " " }}
                </li>
            </ul>
            <h3>Введіть рік для показу старших користувачів</h3>
            <SearchForm @setAgeProp='setAge'></SearchForm>
            <ul>
                <li v-for="user in newUsers" :key="user.id">
                    {{ user.firstName + " " + user.age + " років" }}
                </li>
            </ul>
            <p>Середній вік {{ arithmeticMeanUsersAge }}</p>
        </div>
        <div>
            <Title :textValue="'Список хто без дітей'"> </Title>
            <ul>
                <li v-for="user in filterChildFree" :key="user.id">
                    {{ user.firstName + " " + user.lastName }}
                </li>
            </ul>
        </div>
    </div>
</template>
  


<script>
import Title from ".//components/Title.vue";
import SearchForm from "./components/SearchForm.vue";

export default {
    components: {
        Title,
        SearchForm,
    },
    data() {
        return {
            page: 1,
            isShowTitle: true,
            user: {
                id: this.getId(),
                firstName: 'Christopher',
                lastName: 'Anderson',
                age: 29,
                address: { city: "kuiv", index: 32432 },
                children: true,
            },
            users: [
                {
                    id: this.getId(),
                    firstName: 'John',
                    lastName: 'Doe',
                    age: 30,

                    address: { city: "kuiv", index: 32432 },
                    children: false,

                },
                {
                    id: this.getId(),
                    firstName: 'Jane',
                    lastName: 'Smith',
                    age: 25,

                    address: { city: "kuiv", index: 32432 },
                    children: true,

                },
                {
                    id: this.getId(),
                    firstName: 'Michael',
                    lastName: 'Johnson',
                    age: 35,

                    address: { city: "kuiv", index: 32432 },
                    children: true,

                },
                {
                    id: this.getId(),
                    firstName: 'Sarah',
                    lastName: 'Williams',
                    age: 28,

                    address: { city: "kuiv", index: 32432 },
                    children: false,

                },

            ],
            newHTTPUsers: [],
            newUsers: [],
            country: '',
            countryList: []
        };
    },
    methods: {
        getId() {
            return "id" + Math.random().toFixed(3).toString(16).slice(2)
        },

        changeShowTitle() {
            this.isShowTitle = !this.isShowTitle;
        },

        filterUsersByAge(age) {
            this.newUsers = age ? this.users.filter(user => user.age >= age) : this.users
        },

        changeUserAge(e) {
            if (e.target.id === 'btn-1') {
                this.user.age++;
                return
            }
            if (e.target.id === 'btn-2') {
                this.user.age--;
                return
            }
        },

        setAge(age) {
            this.filterUsersByAge(age)
        },

        addUsers() {
            fetch(`https://624a92c2fd7e30c51c0f021d.mockapi.io/contacts/users?limit=3&page=${this.page}`, {
                method: 'GET',
                headers: { 'content-type': 'application/json' },
            }).then(res => {
                this.page++
                if (res.ok) {
                    return res.json();
                }
                console.log(res);
                throw new Error(res.status);
            }).then(users => {
                this.newHTTPUsers = [...users]
            }).catch(error => {
                console.log(error);
            })
        },

        createNewUser() {
            this.user = {
                id: this.getId(),
                firstName: '',
                lastName: '',
                age: null,
                address: { city: "", index: null },
                children: false,
            }
        },

        fetchCountries(name) {
            return fetch(`https://restcountries.com/v3.1/name/${name}?fields=name,flags,languages,capital,population `)
                .then(response => {
                    if (!response.ok) {
                        throw new Error(response.status);
                    }
                    return response.json();
                })
        }
    },
    computed: {
        combineUsers() {
            this.users.push(...this.newHTTPUsers)
            if (this.newHTTPUsers.length !== 0) {
                this.newHTTPUsers = []
            }
            return this.users
        },

        filterChildFree() {

            return this.users.filter(user => !user.children)
        },

        arrayUserAge() {

            return this.users.map(user => user.age)
        },

        arithmeticMeanUsersAge() {
            const arr = this.arrayUserAge
            return (arr.reduce((sum, num) => (sum + num), 0) / arr.length).toFixed(0);
        }
    },

    watch: {

        user(prevUser, newUser) { console.log("Зміни в об'єкті виявлені!"); },
        "user.age"(prevAge, newAge) { console.log("відбулися зміни властивості 'age'"); },
        arithmeticMeanUsersAge(prevAge, newAge) {
            alert('відбулися зміни у computed property');
        },

        user: {
            handler(newObj, oldObj) {
                console.log("Зміни в об'єкті виявлені!");
                console.log("Новий об'єкт:", newObj);
                console.log("Старий об'єкт:", oldObj);
            },
            deep: true
        },

        country(newText, oldText) {
            const searchRequest = newText.trim();
            if (searchRequest !== "") {
                this.fetchCountries(searchRequest)
                    .then(data => {

                        this.countryList = data.map(item => item.name.common);

                    })
                    .catch(error => {

                        console.log(error);
                    });
            } else {
                this.countryList = ["Масив пустий"];
            }
        },

        page: {
            handler(newNum, oldNum) {
                console.log(`Значення 'page' було змінено з ${oldNum} на ${newNum}`);
            },
            immediate: true // Включаємо опцію immediate
        }

    }
}
</script>


<style scoped>
h3 {
    margin: 0;
}



.name-box {
    display: flex;

}

.user {
    padding: 10px;
    background-color: #7fffd44f;
}

button {
    height: fit-content;
    padding: 4px 10px;
    margin: 2px;
}

label {
    display: flex;
    width: 150px;
    margin-bottom: 5px;
}

.form-user {
    display: flex;
    flex-direction: column;
    width: 250px;
}

.bold {
    font-weight: bold;
}

.box-border {
    border: 1px solid #7fffd4e5;
    padding: 4px;
    margin: 2px;
}

.list-wrap {
    display: flex;
    list-style: none;
    margin: 3px;
    padding: 0;
    justify-content: space-between;
    flex-wrap: wrap;
}

.box-wrap {
    display: flex;
    margin: 3px;
    padding: 0;
    justify-content: space-between;
    flex-wrap: wrap;
}

.box-colunm {
    display: flex;
    flex-direction: column;
}
</style>
