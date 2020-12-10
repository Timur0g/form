<template>
<div class="document">
    <div class="form_group">
        <label>Тип документа</label>
        <select class="valid" v-model="documentType"> 
            <option v-for="document in documents" :key="document">{{ document }}</option>
        </select>
    </div>

    <div v-if="documentType === 'Паспорт'" class="pasport">

        <div class="form_group">
            <label>Серия паспорта</label>
            <input 
                type="number" 
                :class="$v.pasport.series.$dirty && $v.pasport.series.$error ? 'invalid' : 'valid'" 
                placeholder="Серия паспорта" 
                v-model.trim="$v.pasport.series.$model"
            />

            <span class="error" v-if="$v.pasport.series.$dirty && !$v.pasport.series.minLength || !$v.pasport.series.maxLength">Серия паспорта должна состоять из четырёх цифр</span>
        </div>

        <div class="form_group">
            <label>Номер паспорта</label>
            <input 
                type="number" 
                :class="$v.pasport.number.$dirty && $v.pasport.number.$error ? 'invalid' : 'valid'" 
                placeholder="Номер паспорта" 
                v-model.trim="$v.pasport.number.$model"
            />

            <span class="error" v-if="$v.pasport.number.$dirty && !$v.pasport.number.minLength || !$v.pasport.number.maxLength">Номер паспорта должен состоять из шести цифр</span>
        </div>
    </div>

    <div v-if="documentType === 'Свидетельство о рождении'" class="birthСertificate">

        <div class="form_group">
            <label>Серия свидетельства о рождении (первые римские цифры)</label>
            <select v-model.trim="$v.birthСertificate.series.$model">
                <option v-for="numeral in romanNumerals" :key="numeral">{{ numeral }}</option>
            </select>
        </div>

        <div class="form_group">
            <label>Серия свидетельства о рождении (две буквы кириллицы)</label>
            <input 
                id="series_cyrillic"
                type="text" 
                :class="$v.birthСertificate.cyrillic.$dirty && $v.birthСertificate.cyrillic.$error ? 'invalid' : 'valid'" 
                placeholder="Две буквы кириллицы. Например: 'КС' или 'ВП'" 
                v-model.trim="$v.birthСertificate.cyrillic.$model"
            />

            <span class="error" v-if="$v.birthСertificate.cyrillic.$dirty && !$v.birthСertificate.cyrillic.minLength || !$v.birthСertificate.cyrillic.maxLength">Должно быть две буквы</span>
            <span class="error" v-if="$v.birthСertificate.cyrillic.$dirty && !$v.birthСertificate.cyrillic.isCyrillic">Разрешено использовать только кириллицу</span>

        </div>

        <div class="form_group">
            <label>Номер свидетельства о рождении</label>
            <input 
                type="number" 
                :class="$v.birthСertificate.number.$dirty && $v.birthСertificate.number.$error ? 'invalid' : 'valid'" 
                placeholder="Номер свидетельства о рождении" 
                v-model.trim="$v.birthСertificate.number.$model"
            />

            <span class="error" v-if="$v.birthСertificate.number.$dirty && !$v.birthСertificate.number.minLength || !$v.birthСertificate.number.maxLength">Номер свидетельства о рождении должен состоять из шести цифр</span>
        </div>

        

    </div>

    <div v-if="documentType === 'Водительское удостоверение'" class="driversLicense">

        <div class="form_group">
            <label>Серия водительского удостоверения</label>
            <input 
                type="number" 
                :class="$v.driversLicense.series.$dirty && $v.driversLicense.series.$error ? 'invalid' : 'valid'" 
                placeholder="Серия водительского удостоверения" 
                v-model.trim="$v.driversLicense.series.$model"
            />

            <span class="error" v-if="$v.driversLicense.series.$dirty && !$v.driversLicense.series.minLength || !$v.driversLicense.series.maxLength">Серия водительского удостоверения должна состоять из четырёх цифр</span>
        </div>

        <div class="form_group">
            <label>Номер водительского удостоверения</label>
            <input 
                type="number" 
                :class="$v.driversLicense.number.$dirty && $v.driversLicense.number.$error ? 'invalid' : 'valid'" 
                placeholder="Номер водительского удостоверения" 
                v-model.trim="$v.driversLicense.number.$model"
            />

            <span class="error" v-if="$v.driversLicense.number.$dirty && !$v.driversLicense.number.minLength || !$v.driversLicense.number.maxLength">Номер водительского удостоверения должен состоять из шести цифр</span>
        </div>
    </div>


    <div class="form_group">
            <label>Выдан</label>
            <input 
                type="text" 
                :class="$v.issued.$dirty && $v.issued.$error ? 'invalid' : 'valid'" 
                placeholder="Выдан" 
                v-model.trim="$v.issued.$model"
            />
            <span class="error" v-if="$v.issued.$dirty && !$v.issued.minLength">Поле должно содержать в себе минимум три буквы</span>
    </div>

    <div class="form_group">
        <label>Дата выдачи*</label>

        <input 
        type="date" 
        :class="$v.dateOfIssue.$dirty && $v.dateOfIssue.$invalid ? 'invalid' : 'valid'" 
        :min="minDateFormat"
        :max="maxDateFormat"
        v-model="$v.dateOfIssue.$model"/>

        <span class="error" v-if="$v.dateOfIssue.$dirty && !$v.dateOfIssue.required">Это обязательное поле</span>
        <span class="error" v-if="$v.dateOfIssue.$dirty && !$v.dateOfIssue.dateValidate">Невалидные данные</span>
    </div>

    <div class="buttons">
        <button :disabled="page === 1" @click="changeButton('buttonBack')"> Назад</button>
        <button :disabled="$v.$invalid" @click="changeButton('buttonNext')"> Завершить </button>
    </div>


</div>
</template>

<script>
import { validationMixin } from 'vuelidate'
const {required, minLength, maxLength, helpers } = require("vuelidate/lib/validators");

const date = new Date();
const curr_date = date.getDate();
const curr_month = date.getMonth() + 1;
const curr_year = date.getFullYear();

export default {
    name: "Document",
    mixins: [validationMixin],
    props: {
        page: Number
    },
    data() {
        return {
            documentType: 'Паспорт',
            documents: ["Паспорт", "Свидетельство о рождении", "Водительское удостоверение"],
            maxDateFormat: `${curr_year}-${curr_month <= 9 ? "0" + curr_month : curr_month}-${curr_date <= 9 ? "0" + curr_date : curr_date}`,
            minDateFormat: '1900-01-01',
            romanNumerals: ['I', 'II', 'III'],

            pasport: {
                series: '',
                number: ''
            },

            birthСertificate: {
                series: '',
                cyrillic: '',
                number: ''
            },


            driversLicense: {
                series: '',
                number: ''
            },


            dateOfIssue: '',
            issued: ''
        }
    },
    validations: {
        pasport: {
            series: {
                minLength: minLength(4),
                maxLength: maxLength(4) 
            },

            number: {
                minLength: minLength(6),
                maxLength: maxLength(6) 
            }
        },

        birthСertificate: {
            series: {
                minLength: minLength(4),
                maxLength: maxLength(4) 
            },

            cyrillic: {
                minLength: minLength(2),
                maxLength: maxLength(2),
                isCyrillic: helpers.regex('isCyrillic', /[а-яА-ЯёЁ]/)
            },

            number: {
                minLength: minLength(6),
                maxLength: maxLength(6) 
            }
        },

        driversLicense: {
            series: {
                minLength: minLength(4),
                maxLength: maxLength(4) 
            },

            number: {
                minLength: minLength(6),
                maxLength: maxLength(6) 
            }
        },

        issued: {
            minLength: minLength(3),
            maxLength: maxLength(30) 
        },

        dateOfIssue: {
            required, 
            dateValidate(value) {
                const [year, mounth, date] = value.split("-");
                return year < 1900 || year > curr_year || (year === 1900 && mounth > 1 && date > 1) ? false : true;
            },
        }

    },

    methods: {
        changeButton(button) {
            this.$emit('changeButton', button, {
                documentType: this.documentType,
                dateOfIssue: this.dateOfIssue,
                issued: this.issued,
                document: this.documentType === 'Паспорт' ? this.pasport : this.documentType === 'Свидетельство о рождении' ? this.birthСertificate : this.documentType === 'Водительское удостоверение' ? this.driversLicense : null,
            }, true)
        },
    }
}
</script>

<style>

#series_cyrillic {
    text-transform: uppercase;
}
    
</style>