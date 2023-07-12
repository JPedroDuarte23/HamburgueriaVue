<template>
    <div>
        <Message :msg="msg" v-show="msg"/>
        <div>
            <form action="" id="burger-form" @submit="createBurger">
                <div class="input-container">
                    <label for="nome">Nome do cliente:</label>
                    <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
                </div>
                <div class="input-container">
                    <label for="pao">Escolha o pão:</label>
                    <select name="pao" id="pao" v-model="pao">
                        <option value="">Selecione o seu pao</option>
                        <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
                    </select>
                </div>
                <div class="input-container">
                    <label for="carne">Escolha a carne do seu burger:</label>
                    <select name="carne" id="carne" v-model="carne">
                        <option value="">Selecione o seu tipo de carne</option>
                        <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
                    </select>
                </div>
                <div class="input-container" id="opcionais-container">
                    <label for="opcionais" id="opcionais-title">Selecione os opcionais</label>
                    <div v-for=" opcional in opcionaisData " :key="opcional.id" class="checkbox-container">
                        <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                        <span>{{ opcional.tipo }}</span>
                    </div>
                </div>
                <div class="input-container">
                    <input type="submit" value="Criar o seu burger" class="submit-btn">
                </div>
            </form>
        </div>
    </div>
</template>

<script>
import Message from './Message.vue';

export default {
    name: 'BurgerForm',
    data() {
        return {
            //plural, que vem do backend
            paes: null,
            carnes: null,
            opcionaisData: null,
            // singulares, que vem do formulário
            nome: null,
            pao: null,
            carne: null,
            opcionais: [],
            status: "Solicitado",
            msg: null
        }
    },
    components: {
        Message
    },
    methods: {
        async getIngredients() {

            const req = await fetch('http://localhost:3000/ingredientes');
            const data = await req.json();

            this.paes = data.paes;
            this.carnes = data.carnes;
            this.opcionaisData = data.opcionais;
        },
        async createBurger(event) {

            event.preventDefault();

            const data = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                opcionais:  Array.from(this.opcionais),
                status: "Solicitado"
            };
            if(data.nome != null && data.carne != null && data.pao != null) {

            const dataJson = JSON.stringify(data);

            const req = await fetch("http://localhost:3000/burgers", {
                method: "POST",
                headers: {"Content-Type": "application/json"},
                body: dataJson
        });
        const res = await req.json();

        //Colocar uma mensagem no sistema
        this.msg = `Pedido Nº${res.id} realizado com sucesso`
        //limpar mensagem
        setTimeout(() => {
         this.msg = ""   
        }, 5000);
        //limpar os campos 
        this.nome = "";
        this.carne = "";
        this.pao = "";
        this.opcionais = []
        } else {
            this.msg = `Preencha todos os campos corretamente!`
            setTimeout(() => {
                this.msg = ""
            }, 5000)
        }
    } 
    },
    mounted() {
        this.getIngredients()
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

.checkbox-container span ,
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
    transition: .5s;
    cursor: pointer;
}

.submit-btn:hover {
    background-color: transparent;
    color: #222;
}

</style>