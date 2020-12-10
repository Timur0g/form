<template>
  <div class="address">
      <div class="form_group">
        <label>Ваш город* адрес и индекс</label>
        <input 
        type="search" 
        :class="$v.address.$dirty && $v.address.$error ? 'invalid' : 'valid'" 
        placeholder="Адрес*" 
        v-model.trim="$v.address.$model"
        @input="getAdressAjax"
        />

        <span class="error" v-if="$v.address.$dirty && !$v.address.required">Это обязательное поле</span>

        <div v-if="address.length > 0" class="city_active" @click="setUserAddress('')">{{ address }}</div>

        <hr>

        <div class="cities" v-if="address.length > 0">
            <div :class="address === city.unrestricted_value ? 'city_active' : 'city'" @click="setUserAddress(city.unrestricted_value)" v-for="city in addressArrayAJAX" :key="city.unrestricted_value"> {{ city.unrestricted_value }}</div>
            <div v-if="notFound">По вашему запросу ничего не найдено</div>
        </div>
      </div>

      <div class="buttons">
        <button :disabled="page === 1" @click="changeButton('buttonBack')"> Назад</button>
        <button :disabled="$v.$invalid" @click="changeButton('buttonNext')"> Вперед </button>
      </div>
    </div>
</template>

<script>
import { validationMixin } from 'vuelidate'
const { required } = require("vuelidate/lib/validators");

export default {
    name: "Adress",
    mixins: [validationMixin],
    props: {
        page: Number
    },
    data() {
        return {
            address: '',
            addressArrayAJAX: [],
            notFound: false
        }
    },

    methods: {
        changeButton(button) {
            this.$emit('changeButton', button, {
                address: this.address,
            })
        },
        setUserAddress(value) {
            this.address = value
        },
        getAdressAjax() {
            const url = "https://suggestions.dadata.ru/suggestions/api/4_1/rs/suggest/address";
            const token = "1dea2a31b873fe51d324a232a8bd1200214419a2";
            const query = this.address;

            const options = {
                method: "POST",
                mode: "cors",
                headers: {
                "Content-Type": "application/json",
                Accept: "application/json",
                Authorization: "Token " + token,
                },
                body: JSON.stringify({ query: query }),
            };

        fetch(url, options)
            .then((response) => response.text())
            .then((result) => {
                if(JSON.parse(result).suggestions.length === 0 && [...this.address].length > 0) {
                    this.addressArrayAJAX = []
                    this.notFound = true
                }else {
                   this.addressArrayAJAX = JSON.parse(result).suggestions
                   this.notFound = false
                }
            })
                
            .catch((error) => console.log("error", error));
            }
    },
    validations: {
        address: {
            required
        }
    }
}
</script>

<style>

.cities {
    margin-top: 30px;
    background-color: rgba(102, 51, 153, 0.13);
    padding: 50px;
    text-align: center;
    border-radius: 150px;
    box-sizing: border-box;
}

.city, .city_active {
    padding: 10px;
    border-radius: 150px;
    margin: 10px auto;
    cursor: pointer;
    text-align: center;
    min-width: 300px;
    box-sizing: border-box;
}

.city {
    background-color: rgba(65, 105, 225, 0.226);
}

.city:hover {
    background-color: rgba(65, 105, 225, 0.459);
}

.city_active:hover {
    background-color: rgba(0, 255, 13, 0.527);
}

.city_active {
    background-color: rgba(0, 255, 13, 0.411);
}

@media screen and (max-width: 450px){
    .cities  {
        width: 100%;
        min-width: 100%;
        border-radius: 0px;
        background-color: white;
        padding: 5px;
    }
    .city, .city_active{
        min-width: 150px;
        font-size: 10px;
    }
}

</style>