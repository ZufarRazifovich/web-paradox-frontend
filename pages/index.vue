<template>
  <b-container>
    <b-row>
      
      <b-col cols="12" class="pt-5">
        <b-form-textarea
          id="textarea"
          v-model="text"
          placeholder="Введите доменное имя, а затем нажмите клавишу enter"
          rows="3"
          max-rows="6"
        ></b-form-textarea>

      
      </b-col>
      <b-col cols="12" class="mt-2 ml-1">     
        <b-button   @click="newDomainName(); " >
          Проверить доменные имена  
        </b-button>
      </b-col>
      <b-col cols="12">
        <p v-for="item in domainsListing">{{item}}</p>
        <b-table striped hover :items="domainsListing"></b-table>

      </b-col>
    </b-row>
  </b-container>
  
</template>

<script>
  import axios from 'axios'
  export default {
    name: 'indexPage',
    data() {
      return {
        text: '',
        textOut: '',
        domainsListing: [],
        visibleTable: false,
        enabledButton: true,
      }
    },
    methods:{
      newDomainName(){
        this.enabledButton = false;
        var domainsListing = [];
        var domainsList = this.text.split("\n");
        domainsList.forEach(function callback (element, index)  {
          
          var re = /^([\wёa-я-]{2,}\.)+[\wёa-я-]{2,}$/i;
          var validate = re.test(String(element).toLowerCase())

          if(validate){
            axios.post('http://127.0.0.1:8080/api/createNews',{
              domainName: element,
            }).then(response=>{
              domainsListing[index] = {
              'domain': element,
              'validationStatus': 'ok', 
              'start date of registration': response.data.domainCreated,
              'isFreedom': response.data.isFredom
            };
            }).catch(error=>{

            })
          }else{
            domainsListing[index] = {
              'domain': element,
              'validationStatus': 'not ok', 
              'start date of registration': '----',
              'isFreedom': '---'
            };
          }
        })
        this.enabledButton = true;
        this.visibleTable = false;
        // setTimeout(this.domainsListing = domainsListing, 3000);
        setTimeout(() => this.domainsListing = domainsListing, 3000);
        

        
      }
    },
    components:{
    axios,
    }
  }
</script>