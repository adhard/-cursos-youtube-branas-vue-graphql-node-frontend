<template>
  <div>
    <div id="slogan" class="text-center">
      <h1>NameGator <i class="fa fa-check"></i> </h1>
      <h6 class="text-secondary">Gerador de nomes</h6>
      <a href="https://www.youtube.com/watch?v=TSX_hMfL13U&t=666s">Link no Youtube (Rodrigo Branas)</a>
    </div>
    <div id="main">
      <DomainList />
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css"
import "font-awesome/css/font-awesome.css"
import DomainList from "./components/DomainList"

export default {
	name: "App",
	components: {
		DomainList
	}
}
</script>

<style>

  #slogan {
    margin-top: 30px;
    margin-bottom: 30px;
  }

  #main {
    background: #f1f1f1;
    padding-top: 30px;
    padding-bottom: 30px;
  }

</style>
