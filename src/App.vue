<script setup lang="ts">
import { onBeforeMount, onMounted, ref } from "vue";



interface Item {
  userId:string,
  id:string,
  title:string,
  body:string
}

const data= ref<Item[]> ([]);
const openID = ref<Number | null>(null);
const count = ref(0);
const currentHeight = ref(0);
const modalOpen = ref(false);
const modalHeader = ref('');
const modalText = ref('');

onMounted(() => {
  fetch('https://jsonplaceholder.typicode.com/posts')
  .then(response => response.json())
  .then(json => {data.value = json.slice(0,5)})
})



const getSlideHeight = (s:HTMLElement) => {
	  let slideHeight = 0;
	  Array.from(s.children).forEach(child=>{
	    const style = window.getComputedStyle(child) as {
        marginTop: string;
        marginBottom: string;
        borderWidth: string;
      };
	    const childHeight = child.scrollHeight + parseInt(style.marginTop || '0', 10) + parseInt(style.marginBottom || '0', 10) + parseInt(style.borderWidth || '0', 10);
	      slideHeight += childHeight
	  });
	  return slideHeight;
  }

const handleToggle = (event: Event, index:number) => {
  let count = 0;
  if(event.target){
    let t = event.target as HTMLElement;
    let el = t.parentNode!.parentNode!.querySelector('.faq__content') as HTMLElement;
    if(index === openID.value){
    openID.value = null
    currentHeight.value = 0;
  }else{
    openID.value = index;
    currentHeight.value = getSlideHeight(el);
  }
  }
}

const toggleModal = (isBody:boolean, item:Item|null=null) => {
  if(isBody){
    let body = document.getElementsByTagName('body')[0]!;
    body.addEventListener('click', () => {
      if(!modalOpen){
        return
      }else{
        modalOpen.value = false;
      }
    })
  }else{
    modalHeader.value = item!.title;
    modalText.value = item!.body;
    modalOpen.value = true;
  }
}

</script>
<template>
  <main :style="{filter: modalOpen ? 'blur(3px)':''}" @click="toggleModal(true)">
    <div class="faq__container">
      <div class="top">
        <h2>Frequently asked questions</h2>
        <p>
          Lorem ipsum dolor sit amet consectetur adipisicing elit. 
          Debitis, perferendis sed quos enim illum ad, minus nisi nostrum nihil accusantium 
          soluta nam alias? Reiciendis, architecto debitis tenetur autem totam laboriosam!
        </p>
      </div>
      <button class="faq__toggler" v-for="(item, index) in data" :key="index" @click="(event) => handleToggle(event, index)">
          <div class="faq__header">
            <div :style="{'padding-right':'1rem'}">{{ item.title }}</div>
            <div>
                <svg :style="{'pointer-events':'none','transition':'transform .3s ease .2s', 'transform': data.indexOf(item) === openID ? '':'rotate(45deg)'}" xmlns="http://www.w3.org/2000/svg" fill="none" stroke="black" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
            </div>
          </div>
        <div class="faq__content" :style="{height: data.indexOf(item) === openID ? `${currentHeight}px`:'0px'}">
          <div>
            {{ item.body }}
            <div :style="{'margin-top':'2rem'}"> 
              <button class="btn" @click.stop="toggleModal(false, item)">More</button>
            </div>
          </div>
        </div>
      </button>
    </div>
  </main>
  <div class="modal" :class="modalOpen ? 'isOpen':''">
    <div> 
      <h2>{{ modalHeader }}</h2>
     <p>
      {{ modalText }}
     </p>
    </div>
  </div>
</template>

<style scoped>
body{
  transition: all .3s ease;
  font-size: 14px;
}

h2{
  line-height: 1.2;
  margin-bottom:1rem;
}

.faq__container{
  max-width: 900px;
  margin:auto;
}
.top{
  text-align: center;
  margin-bottom: 6rem;
}
.top h2{
  font-size: 3rem;
}
.faq__item{
  background-color: #e1dada;
  padding: 1rem;
  margin-bottom: 20px;

}
button.faq__toggler{
  appearance: none;
  background: transparent;
  border: none;
  background:#f5f7fa;
  width: 100%;
  text-align: left;
  padding:1rem 1.5rem;
  margin-bottom:0.5rem
}

.faq__header{
  display: flex;
  justify-content: space-between;
  font-size: 1.25rem;
  font-weight: 900;
  align-items: center;
}

.faq__header svg{
  height: 1.2rem;
  width: 1.2rem;
}

.faq__content{
  transition: all .6s ease;
}
.faq__content > div{
  margin: 2rem 0rem;
  padding-right: 2rem;
}

.faq__content:not(.open){
  overflow: hidden;
  height: 0;
}
.modal{
  pointer-events: none;
  opacity: 0;
  position: fixed;
  top:50%;
  left: 50%;
  transform: translate(-50%, -60%);
  max-width: 600px;
  background: white;
  border-radius: 2rem;
  min-width: 400px;
  aspect-ratio: 1;
  padding: 2rem;
  transition: transform .6s ease;
}

.modal.isOpen{
  pointer-events: all;
  opacity: 1;
  transform: translate(-50%, -50%);
}

button.btn{
  border: none;
  background: #2500f9;
  appearance: none;
  padding: 0.8rem 2rem;
  border-radius: 3rem;
  transition: all .3s ease;
  color: white;
  font-weight: 900;
}
button.btn:hover{
  background:#1f00cd
}
</style>
