<template>
  <main id="login-container">
    <h1 class="title">
      Entre na sua conta
    </h1>
    <form class="form-container">
      <el-form-item label="Usuário">
        <el-input 
          v-model="login.user" 
          placeholder="Digite aqui" 
        />
      </el-form-item>
      <el-form-item label="Senha">
        <el-input
          v-model="login.password"
          type="password"
          placeholder="•••••••••••"
          show-password
        />
      </el-form-item>
      <div class="register-div">
        <span>
          Não possui uma conta?
          <RouterLink to="/register">Registre-se</RouterLink>
        </span>
      </div>
      <el-button color="#7FE7C4"> 
        Entrar 
      </el-button>
    </form>
  </main>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import { 
  ElInput, 
  ElFormItem, 
  ElButton 
} from "element-plus";

interface login { 
  user: string,
  password: string
}

const login: login = reactive({
  user: '',
  password: ''
})

</script>

<style lang="scss">

#login-container {
  width: 60%;
  background-color: #25282b;
  border-radius: 15px;
  border: 5px solid #7fe7c4;
  box-shadow: 0 0 10px 2px #7fe7c4;
  padding: 80px 30px;

  input {
    color: #fff;
  }

  .title {
    font-size: 36px;
    text-align: center;
  }

  .form-container {
    width: 100%;
    margin: 0 auto;
    margin-top: 40px;
    max-width: 326px;

    .el-form-item {
      flex-direction: column;
      margin-bottom: 10px;

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
    }

    .register-div {
      text-align: center;
      margin-top: 20px;

      a {
        text-decoration: none;
        color: #7fe7c4;
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
}

@media (min-height: 502px) {
  #login-container {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
}

@media (max-width: 768px) {
  #login-container {
    width: 90%;

    .title {
      font-size: 5vw;
    }
  }
}

@media (max-width: 480px) {
  #login-container {
    width: 90%;
    padding: 50px 30px;

    .title {
      font-size: 7vw !important;
    }

    .form-container {
      .register-div {
        display: flex;
        justify-content: center;

        span {
          width: 200px;
        }
      }
    }
  }
}

</style>
