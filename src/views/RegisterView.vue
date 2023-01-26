<template>
  <main id="register-container">
    <RouterLink to="/login" class="return-icon">
      <span class="material-symbols-outlined">
        keyboard_return
      </span>
    </RouterLink>
    <h1 class="title">
      Cadastro
    </h1>
    <el-form 
      ref="ruleFormRef" 
      :model="ruleForm" 
      :rules="rules" 
      class="form-container" 
      hide-required-asterisk
    >
      <el-row>
        <el-col :xs="24" :sm="12">
          <el-form-item 
            label="Nome" 
            prop="firstName"
          >
            <el-input 
              v-model="ruleForm.firstName" 
              placeholder="Digite aqui" 
            />
          </el-form-item>
        </el-col>

        <el-col :xs="24" :sm="12">
          <el-form-item 
            label="Sobrenome" 
            prop="lastName"
          >
            <el-input 
              v-model="ruleForm.lastName" 
              placeholder="Digite aqui" 
            />
          </el-form-item>
        </el-col>

        <el-col :xs="24" :sm="24">
          <el-form-item 
            label="Usuário" 
            prop="user"
          >
            <el-input 
              v-model="ruleForm.user" 
              placeholder="Digite aqui" 
            />
          </el-form-item>
        </el-col>

        <el-col :xs="24" :sm="24">
          <el-form-item 
            label="E-mail" 
            prop="email"
          >
            <el-input 
              v-model="ruleForm.email" 
              placeholder="exemplo@exemplo.com" 
            />
          </el-form-item>
        </el-col>

        <el-col :xs="24" :sm="24">
          <el-form-item 
            label="CPF" 
            prop="cpf" 
            :key="`$cpf-${controlRender}`"
          >
            <el-input 
              id="input-cpf"
              :value="ruleForm.cpf" 
              placeholder="123.456.789-09" 
              @focus="addListenerOnlyNumber()"
              @blur="removeListenerOnlyNumber()"
              @input="maskCPF($event)"
            />
          </el-form-item>
        </el-col>
        
        <el-col :xs="24" :sm="12">
          <el-form-item 
            label="Senha" 
            prop="password"
          >
            <el-input 
              v-model="ruleForm.password" 
              type="password"
              placeholder="•••••••••••"
              show-password
            />
          </el-form-item>
        </el-col>

        <el-col :xs="24" :sm="12">
          <el-form-item 
            label="Repita a senha" 
            prop="retypePassword"
          >
            <el-input 
              v-model="ruleForm.retypePassword" 
              type="password"
              placeholder="•••••••••••"
              show-password
            />
          </el-form-item>
        </el-col>

        <el-col :xs="24" :sm="12">
          <el-form-item 
            label="Idade" 
            prop="age"
          >
            <el-input-number
              v-model="ruleForm.age"
              :min="1"
              :max="200"
              controls-position="right"
              size="large"
              placeholder="Digite aqui"
            />
          </el-form-item>
        </el-col>

        <el-col :xs="24" :sm="12">
          <el-form-item label="País" prop="selectedCountry">
            <el-select 
              v-model="ruleForm.selectedCountry" 
              placeholder="Selecione"
              clearable 
              filterable 
            >
              <el-option
                v-for="item in ruleForm.countryOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
          </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item>
        <el-button 
          color="#7FE7C4" 
          @click="submitForm(ruleFormRef)"
        > 
          Cadastrar
        </el-button>
      </el-form-item>
    </el-form>
  </main>
</template>

<script lang="ts" setup>

import { reactive, ref, onMounted } from 'vue';
import type { Ref } from 'vue';
import type { FormInstance, FormRules } from 'element-plus';
import { 
  ElForm,
  ElInput, 
  ElFormItem, 
  ElRow,
  ElCol,
  ElButton,
  ElInputNumber,
  ElSelect,
  ElOption,
} from "element-plus";

onMounted(() => {
  getCountries();
})

interface countryOption {
  value: number,
  label: string
}

const getCountries = (): void => { fetch("https://servicodados.ibge.gov.br/api/v1/paises")
  .then((response: Response) => response.json())
  .then((response: object) => {
    let countries: string[] = response.map<string[]>((item: object): string => item.nome.abreviado);
    let uniqueContries: string[] = [...new Set(countries)];
    uniqueContries.sort();

    ruleForm.countryOptions = uniqueContries.map((item: string, i: number): countryOption => { 
      return {value: i, label: item}
    });
  })
}

const onlynumber = (evt: KeyboardEvent)=> {
  let theEvent = evt || window.event;
  let key: number = theEvent.keyCode || theEvent.which;
  if (key === 46 || key === 8) return;
  if ((key >= 48 && key <= 57) || (key >= 96 && key <= 105)) return;
  theEvent.preventDefault();
}

const ruleFormRef = ref<FormInstance>();
const controlRender: Ref<number> = ref(0);

interface registerForm {
  firstName: string,
  lastName: string,
  user: string,
  email: string,
  cpf: string,
  password: string,
  retypePassword: string,
  age: number | undefined,
  selectedCountry: string | number | undefined,
  countryOptions: object[],
  flagRegister: boolean
}

const ruleForm: registerForm = reactive({
  firstName: '',
  lastName: '',
  user: '',
  email: '',
  cpf: '',
  password: '',
  retypePassword: '',
  age: undefined,
  selectedCountry: undefined,
  countryOptions: [],
  flagRegister: false,
})

const validateRequiredField = (rule: any, value: any, callback: any) => {
  if(ruleForm.flagRegister) {
    if(value === '' || value == null) callback(new Error('Campo obrigatório'))
  }
}

const addListenerOnlyNumber = () => {
    window.addEventListener('keydown', onlynumber, true)
}

const removeListenerOnlyNumber = () => {
  window.removeEventListener('keydown', onlynumber, true)
}

const validateCPF = (rule: any, value: any, callback: any) => {
  if(ruleForm.flagRegister) {
    if(value === '') callback(new Error('Campo obrigatório'))
  
    let sum: number;
    let rest: number;
    sum = 0;
    value = value.replace(/[.-]/g, '');
  
    if (value == "00000000000") callback(new Error('CPF inválido'))
  
    for (let i: number = 1; i<=9; i++) sum = sum + parseInt(value.substring(i-1, i)) * (11 - i);
    rest = (sum * 10) % 11;
  
    if ((rest == 10) || (rest == 11))  rest = 0;
    if (rest != parseInt(value.substring(9, 10)) ) callback(new Error('CPF inválido'))
  
    sum = 0;
    for (let i = 1; i <= 10; i++) sum = sum + parseInt(value.substring(i-1, i)) * (12 - i);
    rest = (sum * 10) % 11;
  
    if ((rest == 10) || (rest == 11))  rest = 0;
    if (rest != parseInt(value.substring(10, 11) ) ) callback(new Error('CPF inválido'))
  }
}

const maskCPF = (value: string) => {
  if(value.length > 14) 
    return controlRender.value += 1;

  ruleForm.cpf = value;
  controlRender.value += 1;

  setTimeout(()=> {
    document.getElementById("input-cpf")?.focus();
  }, 1);
  
  let numbers: string = value.replace(/[.-]/g, '');
  ruleForm.cpf = numbers!.match(/.{1,3}/g).join(".").replace(/\.(?=[^.]*$)/,"-")
}

const validateEmail = (rule: any, value: any, callback: any) => {
  if(ruleForm.flagRegister) {
    if(value === '') {
      callback(new Error('Campo obrigatório'));
    } else {
      let user: string = value.substring(0, value.indexOf("@"));
      let domain: string = value.substring(value.indexOf("@")+ 1, value.length);
      if((user.length >= 1) &&
        (domain.length >= 3) &&
        (user.search("@") == -1) &&
        (domain.search("@") ==- 1) &&
        (user.search(" ") == -1) &&
        (domain.search(" ") == -1) &&
        (domain.search(".") != -1) &&
        (domain.indexOf(".") >=1)&&
        (domain.lastIndexOf(".") < domain.length - 1)) {
          callback();
      } else {
        callback(new Error('E-mail inválido!'))
      }
    }
  }
}

const validateRetypePassword = (rule: any, value: any, callback: any) => {
  if(ruleForm.flagRegister) {
    if(value === '') { 
      callback(new Error('Campo obrigatório'));
    } else {
      ruleFormRef.value?.validateField('retypePassword', () => null)
      if(value !== ruleForm.password && ruleForm.password !== '') {
        callback(new Error('As senhas não são iguais'))
      }
    }
  }
};

const rules = reactive<FormRules>({
  firstName: [
    { 
      validator: validateRequiredField, 
      trigger: 'change'
    },
  ],
  lastName: [
    { 
      validator: validateRequiredField,
      trigger: 'change' 
    },
  ],
  user: [
    {
      validator: validateRequiredField, 
      trigger: 'change',
    },
  ],
  email: [
    {
      validator: validateEmail,
      trigger: 'change',
    },
  ],
  cpf: [
    {
      validator: validateCPF,
      trigger: 'change',
    },
  ],
  password: [
    {
      validator: validateRequiredField, 
      trigger: 'change',
    },
  ],
  retypePassword: [
    {
      validator: validateRetypePassword,
      trigger: 'change',
    },
  ],
  age: [
    {
      validator: validateRequiredField, 
      trigger: 'change',
    },
  ],
  selectedCountry: [
    {
      validator: validateRequiredField, 
      trigger: 'change',
    },
  ],
})

const submitForm = async (formEl: FormInstance | undefined) => {
  ruleForm.flagRegister = true;
  if (!formEl) return
  await formEl.validate((valid) => {
    if (valid) formEl.resetFields()
  });
}

</script>

<style lang="scss">

#register-container {
  width: 60%;
  background-color: #25282b;
  border-radius: 15px;
  border: 5px solid #7fe7c4;
  box-shadow: 0 0 10px 2px #7fe7c4;
  padding: 80px 30px;
  margin: 0 auto;
  position: relative;

  .return-icon {
    position: absolute; 
    top: 30px;
    left: 45px;
    span {
      font-size: 35px;
      color: #fff;
      transition: .7s;
      
      &:hover {
        color: #7fe7c4;
        text-shadow: 1px 1px 5px rgb(116, 116, 116);
      }
    }
  }

  input {
    color: #fff;
  }

  .title {
    font-size: 36px;
    text-align: left;
    padding: 0 20px;
  }

  .form-container {
    width: 100%;
    margin: 0 auto;
    margin-top: 40px;

    .el-form-item {
      flex-direction: column;
      margin-bottom: 10px;
      padding: 0 20px;

      .el-input {
        --el-input-focus-border-color: #7fe7c4;
        --el-input-bg-color: transparent;
        height: 40px;
      }

      .el-form-item__label {
        justify-content: start;
        color: #fff;
        font-weight: 400;
        font-size: 13px;
      }

      .el-input-number--large {
        width: 100%;

        span {
          --el-fill-color-light: #7fe7c4;

          .el-icon {
            --color: #000;
          }
        }

        input {
          text-align: left;
        }
      }

      .el-select {
        width: 100%;
        --el-select-input-focus-border-color: #7fe7c4; 
      }
    }
  }

  .el-button {
    margin-top: 30px;
    width: 100%;
    height: auto;
    span {
      padding: 5px !important;
    }
  }
}

@media (max-width: 768px) {
  #register-container {
    width: 100%;

    .title {
      font-size: 5vw;
    }
  }
}

@media (max-width: 480px) {
  #register-container {
    .title {
      font-size: 7vw !important;
    }
  }
}

</style>