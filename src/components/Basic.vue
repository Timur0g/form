<template>
  <div class="basic">
      <div class="form_group">
        <label>Имя*</label>
        <input 
        type="text" 
        :class="$v.name.$dirty && $v.name.$error ? 'invalid' : 'valid'" 
        placeholder="Имя*" 
        v-model.trim="$v.name.$model"/>

        <span class="error" v-if="$v.name.$dirty && !$v.name.required">Это обязательное поле</span>
        <span class="error" v-if="$v.name.$dirty && !$v.name.maxLength">Имя не должно содержать больше двадцати букв</span>
        <span class="error" v-if="$v.name.$dirty && !$v.name.minLength">Имя должно иметь больше двух букв</span>
        <span class="error" v-if="$v.name.$dirty && !$v.name.isLetter">Имя должно содержать только русские или английские буквы без спец символов и пробелов</span>
      </div>

      <div class="form_group">
        <label>Фамилия*</label>
        <input 
        type="text" 
        :class="$v.surname.$dirty && $v.surname.$invalid ? 'invalid' : 'valid'" 
        placeholder="Фамилия*" 
        v-model.trim="$v.surname.$model"/>

        <span class="error" v-if="$v.surname.$dirty && !$v.surname.required">Это обязательное поле</span>
        <span class="error" v-if="$v.surname.$dirty && !$v.surname.maxLength">Фамилия не должна содержать больше двадцати букв</span>
        <span class="error" v-if="$v.surname.$dirty && !$v.surname.minLength">Фамилия должна иметь больше двух букв</span>
        <span class="error" v-if="$v.surname.$dirty && !$v.surname.isLetter">Фамилия должна содержать только русские или английские буквы без спец символов и пробелов</span>
      </div>

      <div class="form_group">
        <label>Отчество</label>
        <input 
        type="text" 
        :class="$v.patronymic.$dirty && $v.patronymic.$invalid ? 'invalid' : 'valid'" 
        placeholder="Отчество" 
        v-model.trim="$v.patronymic.$model"/>

        <span class="error" v-if="$v.patronymic.$dirty && !$v.patronymic.maxLength">Отчество не должно содержать больше двадцати букв</span>
        <span class="error" v-if="$v.patronymic.$dirty && !$v.patronymic.minLength">Отчество должно иметь больше двух букв</span>
        <span class="error" v-if="$v.patronymic.$dirty && !$v.patronymic.isLetter">Отчество должно содержать только русские или английские буквы без спец символов и пробелов</span>
      </div>

      <div class="form_group">
        <label>Дата рождения*</label>
        <input 
        type="date" 
        :class="$v.birthDay.$dirty && $v.birthDay.$invalid ? 'invalid' : 'valid'" 
        :min="minDateFormat"
        :max="maxDateFormat"
        v-model.trim="$v.birthDay.$model"/>

        <span class="error" v-if="$v.birthDay.$dirty && !$v.birthDay.required">Это обязательное поле</span>
        <span class="error" v-if="$v.birthDay.$dirty && !$v.birthDay.dateValidate">Невалидные данные</span>
      </div>

      <div class="form_group">
        <label>Номер телефона*</label>
        <input 
        type="tel" 
        :class="$v.phone.$dirty && $v.phone.$error ? 'invalid' : 'valid'" 
        placeholder="Номер телефона*" 
        v-model.trim="$v.phone.$model"
        @focus="setPhoneCode"
        @input="setPhoneCode"
        />

        <span class="error" v-if="$v.phone.$dirty && !$v.phone.required">Это обязательное поле</span>
        <span class="error" v-if="$v.phone.$dirty && !$v.phone.isNumber">Телефон должен содержать в себе только цифры</span>
        <span class="error" v-if="$v.phone.$dirty && !$v.phone.lengthValidate">Должно быть десять цифр после <b>"+7"</b></span>
      </div>

      <div class="form_group">
        <label>Группа клиентов*</label>
        <select :class="$v.clientGroupUser.$dirty && $v.clientGroupUser.$error ? 'invalid' : 'valid'" v-model="$v.clientGroupUser.$model" multiple> 
        <option v-for="group in clientGroup" :key="group">{{ group }}</option>
        </select>

        <span class="error" v-if="$v.clientGroupUser.$dirty && !$v.clientGroupUser.required">Это обязательное поле</span>
      </div>

      <div class="form_group">
        <label>Лечащий врач</label>
        <select class="valid" v-model="doctorUser"> 
            <option v-for="doctor in doctors" :key="doctor">{{ doctor }}</option>
        </select>
      </div>

      <div class="form_group">
        <label>Не отправлять СМС</label>
        <input type="checkbox" v-model="doNotSendSMS">
      </div>

      <div class="buttons">
        <button :disabled="page === 1 || $v.$invalid" @click="changeButton('buttonBack')"> Назад</button>
        <button :disabled="$v.$invalid" @click="changeButton('buttonNext')"> Вперед </button>
    </div>
  </div>
</template>

<script>
import { validationMixin } from 'vuelidate'
const {
  required,
  minLength,
  maxLength,
  helpers
} = require("vuelidate/lib/validators");

const date = new Date();
const curr_date = date.getDate();
const curr_month = date.getMonth() + 1;
const curr_year = date.getFullYear();


export default {
    name: 'Basic',
    mixins: [validationMixin],
    props: {
        page: Number
    },
    data() {
        return {
            name: '',
            surname: '',
            patronymic: '',
            birthDay: '',
            phone: "",
            clientGroupUser: ["VIP"],
            doctorUser: 'Никто',            
            doNotSendSMS: false,

            maxDateFormat: `${curr_year}-${curr_month <= 9 ? "0" + curr_month : curr_month}-${curr_date <= 9 ? "0" + curr_date : curr_date}`,
            minDateFormat: '1900-01-01',
            clientGroup: ["VIP", "Проблемные", "ОМС"],
            doctors: ['Никто', "Иванов", "Захаров", "Чернышева"]
        }
    },
    methods: {
        setPhoneCode() {
            const [plus, code] = this.phone.split('')
            if(plus !== '+' || code !== '7') {
                this.phone = '+7' + ""
            }
            
        },
        changeButton(button) {
            this.$emit('changeButton', button, {
                name: this.name,
                surname: this.surname,
                patronymic: this.patronymic,
                birthDay: this.birthDay,
                phone: this.phone,
                clientGroupUser: this.clientGroupUser,
                doctorUser: this.doctorUser,      
                doNotSendSMS: this.doNotSendSMS
            })
        }
    },
    validations: {
            name: {
                required,
                minLength: minLength(2),
                maxLength: maxLength(20),
                isLetter: helpers.regex('isLetter', /^[a-zA-Zа-яА-ЯёЁ]*$/)
            },

            surname: {
                required,
                minLength: minLength(2),
                maxLength: maxLength(20),
                isLetter: helpers.regex('isLetter', /^[a-zA-Zа-яА-ЯёЁ]*$/)
            },

            patronymic: {
                minLength: minLength(2),
                maxLength: maxLength(20),
                isLetter: helpers.regex('isLetter', /^[a-zA-Zа-яА-ЯёЁ]*$/)
            },

            birthDay: {
                required,
                dateValidate(value) {
                    const [year, mounth, date] = value.split("-");
                    return year < 1900 || year > curr_year || (year === 1900 && mounth > 1 && date > 1) ? false : true;
                },
            },

            phone: {
                required(value) {
                    let copy = [...value]
                    delete copy[0]
                    delete copy[1]
                    return copy.filter(item => item).length === 0 ? false : true 
                },
                isNumber(value) {
                    let copy = value.split('')
                    delete copy[0] 
                    let str = copy.filter(item => item).join('');
                    let regexp = /^[0-9]+$/;

                    return regexp.test(str)
                },
                lengthValidate(value) {
                    let copy = [...value]
                    delete copy[0]
                    delete copy[1]
                    return copy.filter(item => item).length !== 10 ? false : true 
                }
            },

            clientGroupUser: {
                required
            }
        
    },
}
</script>

<style>

</style>