<template>
   <div class="bg-background border-solid border-2 border-preto2 rounded shadow-lg shadow-preto2-500/50 p-4">
      <h1 class=" text-2xl font-bold  text-preto2">Cadastrar Usuário</h1>
      <hr class="bg-azul2 h-0.5 mt-2 mb-4" />
      <form @submit.prevent="novoUsuario">
         <div class="div_radios">
            <div class="radio-button-container">
               <div class="radio-button">
                  <input type="radio" class="radio-button__input" id="radio1" name="radio-group" value="1"
                     v-model="state.tipo_usuario">
                  <label class="radio-button__label" for="radio1">
                     <span class="radio-button__custom"></span>
                     Veterinário
                  </label>
               </div>
               <div class="radio-button">
                  <input type="radio" class="radio-button__input" id="radio2" name="radio-group" value="2"
                     v-model="state.tipo_usuario">
                  <label class="radio-button__label" for="radio2">
                     <span class="radio-button__custom"></span>
                     Técnico Vet
                  </label>
               </div>
               <div class="radio-button">
                  <input type="radio" class="radio-button__input" id="radio3" name="radio-group" value="3"
                     v-model="state.tipo_usuario">
                  <label class="radio-button__label" for="radio3">
                     <span class="radio-button__custom"></span>
                     Recepcionista
                  </label>
               </div>
               <div class="radio-button">
                  <input type="radio" class="radio-button__input" id="radio4" name="radio-group" value="4"
                     v-model="state.tipo_usuario">
                  <label class="radio-button__label" for="radio4">
                     <span class="radio-button__custom"></span>
                     Visitante
                  </label>
               </div>
            </div>
         </div>
         <div class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-4">
            <div>
               <label>Nome</label>
               <input type="text" placeholder="Digite um nome:" required v-model="state.nome">
            </div>
            <div>
               <label>Sobrenome</label>
               <input type="text" placeholder="Digite um sobrenome:" required v-model="state.sobrenome">
            </div>
            <div>
               <label>Data de Nascimento</label>
               <input type="text" placeholder="Digite uma data:" v-mask-date.br required
                  v-model="state.data_nascimento">
            </div>
            <div>
               <label>CPF</label>
               <input type="text" placeholder="Digite um CPF:" v-mask-cpf required v-model="state.cpf">
            </div>
            <div>
               <label>Gênero</label>
               <select required v-model="state.sexo">
                  <option value="" selected disabled>Selecione</option>
                  <option value="MASCULINO">Masculino</option>
                  <option value="FEMININO">Feminino</option>
                  <option value="OUTROS">Outros</option>
               </select>
            </div>
            <div>
               <label>Telefone</label>
               <input type="text" placeholder="Digite um telefone:" v-mask-phone.br v-model="state.telefone">
            </div>
            <div>
               <label>WhatsApp</label>
               <input type="text" placeholder="Digite um número WhatsApp:" v-mask-phone.br required
                  v-model="state.whatsApp">
            </div>
            <div>
               <label>Email</label>
               <input type="email" placeholder="Digite um email:" required v-model="state.email">
            </div>
            <div>
               <label>Senha</label>
               <input type="password" placeholder="Não é possivel cadastrar uma senha nessa tela" required
                  v-model="state.senha" readonly>
            </div>
            <div>
               <label>Status</label>
               <select v-model="state.status">
                  <option value="ATIVO">Ativo</option>
                  <option value="INATIVO">Inativo</option>
               </select>
            </div>
            <div>
               <label>N° de pets sobre seus cuidados</label>
               <input type="text" disabled v-model="state.n_pets" />
            </div>
            <div v-if="!state.imagem.imagem">
               <label class="form-label">Imagem de Perfil</label>
               <div class="flex items-center space-x-4">
                  <label class="upload-button">
                     <i class="bi bi-camera upload-icon"></i>
                     <span>Selecionar Imagem</span>
                     <input type="file" @change="adicionarImagem" class="hidden">
                  </label>
               </div>
            </div>
            <div v-if="state.imagem.imagem" class="div_img">
               <img :src="state.imagem.imagem" alt="Imagem do Usuário"
                  class="w-28 h-28 object-cover border-2 border-azul4 rounded-full" />
               <button @click="removerImagem"
                  class="bg-vermelho w-6 h-6 rounded-full text-branco hover:bg-vermelho2 duration-300"
                  title="Remover Imagem">
                  <i class="bi bi-x"></i>
               </button>
            </div>
         </div>
         <div>
            <label>Notas Adicionais</label>
            <textarea
               placeholder="Digite aqui informações adicionais, as informações deste campo serão mostradas na tela de perfil do usuário."
               rows="4" v-model="state.notas_adicionais">
            </textarea>
         </div>
         <div class="flex justify-end gap-4 mt-4">
            <BotaoCancel :link="'/usuarios'" :titulo="'Cancelar'" />
            <BotaoSave type="submit" :titulo="'Salvar'" />
         </div>
      </form>
   </div>
   <ModalErro :visible="state.modal" :texto="state.MensagemErro" @update:visible="state.modal = $event" />
   <Loader :loading="state.loader" />
</template>
<script setup>
import { reactive } from 'vue';
import BotaoSave from '@/components/BotaoSave.vue';
import BotaoCancel from '@/components/BotaoCancel.vue';
import services from '@/services';
import { useStorage } from 'vue3-storage';
import Loader from '@/components/Loader.vue';
import ModalErro from '@/components/ModalErro.vue';
import { useRouter } from 'vue-router';

const storage = useStorage();
const token = storage.getStorageSync("token");
const user_tipo = storage.getStorageSync("tipo_usuario");
const router = useRouter();

const state = reactive({
   imagem: {},
   tipo_usuario: null,
   nome: '',
   sobrenome: '',
   data_nascimento: '',
   sexo: '',
   cpf: '',
   telefone: '',
   whatsApp: '',
   email: '',
   status: 'ATIVO',
   n_pets: '',
   senha: '',
   notas_adicionais: '',
   loader: false,
   modal: false,
   MensagemErro: "",
});

async function adicionarImagem(event) {
   const img = event.target.files[0];
   const objImagem = {};
   objImagem.file = img;
   objImagem.imagem = URL.createObjectURL(img);
   state.imagem = objImagem;
}

async function removerImagem() {
   state.imagem = {};
   document.querySelector('input[type="file"]').value = null;
}

function formatarCPF(cpf) {
   return cpf.replace(/[.\-()]/g, "");
}

function formatarTelefone(telefone) {
   return telefone.replace(/[.\-() ]/g, "");
}

function formatarWhatsApp(whatsapp) {
   return whatsapp.replace(/[.\-() ]/g, "");
}

async function novoUsuario() {
   state.loader = true;

   if (user_tipo != 0 && user_tipo != 1) {
      state.MensagemErro = "Você não tem permissão para cadastrar usuários.";
      state.loader = false;
      state.modal = true;
      return;
   }

   const dataRegex = /^(0[1-9]|[12][0-9]|3[01])\/(0[1-9]|1[0-2])\/\d{4}$/;

   if (!dataRegex.test(state.data_nascimento)) {
      state.MensagemErro = "Data inválida. Use o formato DD/MM/AAAA.";
      state.modal = true;
      state.loader = false;
      return;
   }

   let cpfFormatado = formatarCPF(state.cpf);
   let telefoneFormatado = formatarTelefone(state.telefone);
   let whatsappFormatado = formatarWhatsApp(state.whatsApp);

   let formData = new FormData();
   formData.append("tipo_usuario", state.tipo_usuario);
   formData.append("nome", state.nome);
   formData.append("sobrenome", state.sobrenome);
   formData.append("data_nascimento", state.data_nascimento);
   formData.append("sexo", state.sexo);
   formData.append("cpf", cpfFormatado);
   formData.append("telefone", telefoneFormatado);
   formData.append("whatsapp", whatsappFormatado);
   formData.append("email", state.email);
   formData.append("status", state.status);
   formData.append("n_pets", state.n_pets);
   formData.append("senha", cpfFormatado);
   formData.append("notas_adicionais", state.notas_adicionais);
   formData.append("_method", "POST");

   if (state.imagem?.file) {
      formData.append("imagem", state.imagem.file);
   }

   try {
      const response = await services.usuarios.save({ formData, token });
      if (response.status === 200 || response.status === 201) {
         router.push('/usuarios');
      }
   } catch (error) {
      console.error("Erro no cadastro de usuário:", error);

      // Verificar se há múltiplos erros de validação e formatá-los de forma mais amigável
      if (error.response && error.response.data && error.response.data.errors) {
         const errors = Object.values(error.response.data.errors).flat();
         // Usar '\n' para quebrar a linha entre os erros
         state.MensagemErro = errors.length > 1 ? `Ocorreram os seguintes erros:\n${errors.join('\n')}` : `Erro: ${errors[0]}`;
         state.modal = true;
      } else {
         // Mensagem de erro genérica, menos agressiva
         state.MensagemErro = "Não foi possível concluir o cadastro. Tente novamente mais tarde.";
      }
   } finally {
      state.loader = false;
   }
}
</script>
<style scoped>
.radio-button-container {
   display: flex;
   align-items: center;
   gap: 24px;
}

.radio-button {
   display: inline-block;
   position: relative;
   cursor: pointer;
}

.radio-button__input {
   position: absolute;
   opacity: 0;
   width: 0;
   height: 0;
}

.radio-button__label {
   display: inline-block;
   padding-left: 30px;
   margin-bottom: 10px;
   position: relative;
   font-size: 15px;
   color: #1a1b1c;
   font-weight: 600;
   cursor: pointer;
   text-transform: uppercase;
   transition: all 0.3s ease;
}

.radio-button__custom {
   position: absolute;
   top: 0;
   left: 0;
   width: 20px;
   height: 20px;
   border-radius: 50%;
   border: 2px solid #555;
   transition: all 0.3s ease;
}

.radio-button__input:checked+.radio-button__label .radio-button__custom {
   background-color: #066699;
   border-color: transparent;
   transform: scale(0.8);
   box-shadow: 0 0 20px #06669980;
}

.upload-button {
   display: flex;
   align-items: center;
   width: 100%;
   padding: 10px;
   background-color: #066699;
   color: white;
   border-radius: 6px;
   border: 1px solid #ccc;
   cursor: pointer;
   font-size: 14px;
   font-weight: 500;
   text-align: center;
   transition: 0.3s;
}

.upload-button:hover {
   background-color: #055a85;
}

.upload-icon {
   margin-right: 8px;
}

.hidden {
   display: none;
}

.div_img {
   display: flex;
}
</style>