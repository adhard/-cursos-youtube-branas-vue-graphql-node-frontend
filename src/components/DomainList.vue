<template>
  <div>
    
    <div id="main">
      <div class="container">
        <div class="row">
          <div class="col-md">  
            <AppItemList title="Prefixos" type="prefix" :items="items.prefix" v-on:addItem="addItem" v-on:deleteItem="deleteItem" />
          </div>

          <div class="col-md"> 
            <AppItemList title="Sufixos" type="sufix" :items="items.sufix" v-on:addItem="addItem" v-on:deleteItem="deleteItem" />
          </div>

        </div>
        <br/>
        <h5>Domínios <span class="badge badge-info">{{domains.length}}</span></h5>
        <div class="card">
          <div class="card-body">
            <ul class="list-group">
              <li class="list-group-item" v-for="domain in domains" v-bind:key="domain.name">
                <div class="row">
                  <div class="col-md">
                    {{domain.name}}
                  </div>
                  <div class="col-md text-right">
                    <a class="btn btn-info" :href="domain.checkout" target="_blank">
                      <i class="fa fa-shopping-cart"></i>
                    </a>
                  </div>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css"
import "font-awesome/css/font-awesome.css"
import axios from 'axios/dist/axios'
import AppItemList from "./components/AppItemList"

export default {
	name: "App",
	components: {
		AppItemList
	},
	data: function(){
		return {
			items: {
				prefix: [],
				sufix: []
			}
		}
	},
	methods: {
		addItem(item){
			axios({
				url: "http://localhost:4000",
				method: "post",
				data: {
					query: `
						mutation ($item: ItemInput){
							newItem: saveItem(item: $item) {
								id
								type
								description
							}
						}
					`,
					variables: {
						item
					}
				}
			}).then(response => {
				const query = response.data
				const newItem = query.data.newItem
				this.items[item.type].push(newItem)
			})	
		},		
		deleteItem(item){
			axios({
				url: "http://localhost:4000",
				method: "post",
				data: {
					query: `
						mutation ($id: Int) {
							deleted: deleteItem(id: $id)
						}
					`,
					variables: {
						id: item.id
					}
				}
			}).then( () => {
				this.getItems(item.type)
			})
		},		
		getItems(type){
			axios({
				url: "http://localhost:4000",
				method: "post",
				data: {
					query: `
						query ($type: String) {
							prefixes: items(type: $type) {
								id
								type
								description
							}							
						}
					`,
					variables: {
						type
					}
				}
			}).then(response => {
				const query = response.data
				this.items[type] = query.data.items
			})
		}		
	},
	computed: {
		//Computed properties, possuem watchers que só executarão novamente, quando as variaveis que ela depende, no casso
		// de domains, as variaveis são prefixes e sufixes, quando elas mudarem, o codigo domains() é executado
		domains(){
			console.log("Generating domains...")
			const domains = []
			for(const prefix of this.items.prefix){
				for (const sufix of this.items.sufix){
					const name = prefix.description + sufix.description
					const url = name.toLowerCase()
					const checkout = `http://checkout.hostgator.com.br/?a=add&sld=${url}&tld=.com.br`
					domains.push({name, checkout})
				}
			}
			return domains
		}
	},
	created(){
		this.getItems("prefix")
		this.getItems("sufix")
	}
}
</script>

<style>

</style>
