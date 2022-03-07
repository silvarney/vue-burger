<template>
    <Message :msg="msg" v-show="msg"/>
    <div>
        <form id="burger-form" @submit="createBurger">
            <div class="input-container">
                <label for="name">Nome do Cliente:</label>
                <input type="text" name="name" id="name" v-model="name" placeholder="Digite seu nome">
            </div>
            <div class="input-container">
                <label for="bread">Escolha o pão:</label>
                <select name="bread" id="bread" v-model="bread">
                    <option value="">Selecione o seu pão</option>
                    <option v-for="bread in breads" :key="bread.id" :value="bread.type">{{ bread.type }}</option>
                </select>
            </div>
            <div class="input-container">
                <label for="beef">Escolha a carne:</label>
                <select name="beef" id="beef" v-model="beef">
                    <option value="">Selecione a sua carne</option>
                    <option v-for="beef in beefs" :key="beef.id" :value="beef.type">{{ beef.type }}</option>
                </select>
            </div>
            <div id="opcionais-container" class="input-container">
                <label id="opcionais-title" for="optional">Selecione os opcionais:</label>
                <div class="checkbox-container" v-for="optional in optionalsData" :key="optional.id">
                    <input type="checkbox" name="optionals" id="optionals" v-model="optionals" :value="optional.type">
                    <span>{{ optional.type }}</span>
                </div>                
            </div>
            <div class="input-container">
                <input type="submit" class="submit-btn" value="Criar meu Burger!">
            </div>
        </form>
    </div>
</template>

<script>
import Message from './Message.vue';
export default {
    name: 'BurgerForm',
    components: { 
        Message
    },
    data() {
        return {
            breads: null,
            beefs: null,
            optionalsData: null,
            name: null,
            bread: null,
            beef: null,
            optionals: [],
            status: "Solicitado",
            msg: null
        }
    },
    methods: {
        async getIngredientes() {

            const req = await fetch("http://localhost:3000/ingredients");
            const data = await req.json();
            
            this.breads = data.breads;
            this.beefs = data.beefs;
            this.optionalsData = data.optionals;

        },
        async createBurger(e) {
            e.preventDefault();
            
            const data = {
                name: this.name,
                bread: this.bread,
                beef: this.beef,
                optionals: Array.from(this.optionals),
                status: "Solicitado"
            }

            const dataJson = JSON.stringify(data);
    
            const req = await fetch("http://localhost:3000/burgers", {
                method: "POST",
                headers: {"Content-type": "application/json"},
                body: dataJson
            });

            const res = await req.json();

            //mensagem do sistema
            this.msg = `Pedido Nº ${ res.id } realizado com sucesso!`;

            //limpar mensagem
            setTimeout(() => this.msg = "", 3000);

            //limpar campos
            this.name = "";
            this.bread = "";
            this.beef = "";
            this.optionals = "";
        } 
    },
    mounted() {
        this.getIngredientes();
    }
}
</script>

<style scoped>
    #burger-form {
        max-width: 400px;
        margin: 0 auto;
    }

    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    label {
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #FCBA03;
    }

    input, select {
        padding: 5px 10px;
        width: 300px;
    }

    #opcionais-container {
        flex-direction: row;
        flex-wrap: wrap;
    }

    #opcionais-title {
        width: 100%;
    }

    .checkbox-container {
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }

    .checkbox-container span,
    .checkbox-container input {
        width: auto;
    }

    .checkbox-container span {
        margin-left: 6px;
        font-weight: bold;
    }

    .submit-btn {
        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }

    .submit-btn:hover {
        background-color: transparent;
        color: #222;
    }
</style>