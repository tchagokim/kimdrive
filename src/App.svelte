<script>
	import Item from "./Item.svelte"
const api = "https://hogq7j.deta.dev"
let token = ""
let loading = false
let mostrar = false
let sel_file = null
let parent = "/"
let parents = ["/"]
let parents_keys = ["/"]
let path = ""
$ :{
	if (parents.length==0) {
		path = "/"
	} else if (parents.length==1) {
		path = parents[0]
	} else if (parents.length>1) {
		path = parents.slice(1).join("/")
	}
}
let lista = []
let username = ""
let password = ""
let name = ""
let signin = true
let signup_name = ""
let signup_password = ""
let signup_password2 = ""
let signup_username = ""


const signup = () => {
	loading=true
	const json = {
		name : signup_name,
		username : signup_username,
		password : signup_password,
		password2 : signup_password2,
	}
	const options = {
		method: 'POST',
		body: JSON.stringify(json),
		headers: {
			'Accept': 'application/json',
			'Content-Type': 'application/json'
		},
	};
	fetch(`${api}/signup`, options).then(r=>r.json()).then(r=>{
		console.log("SIGNUP R",r)
		alert("Cadastro finalizado. Faça o login")
		location.reload()
	}).finally(()=>loading=false)
	
}


const entrar = () => {
	loading = true
	const json = {
		username : username,
		password : password,
	}
	const options = {
		method: 'POST',
		body: JSON.stringify(json),
		headers: {
			'Accept': 'application/json',
			'Content-Type': 'application/json'
		},
	};
	fetch(`${api}/signin`, options).then(r=>r.json()).then(r=>{
		token = r.token
		console.log(token)
		name = r.name
		listar()
	}).finally(()=>loading=false)
	
}

const voltar = () => {
	const lst = [...parents]
	const lstk = [...parents_keys]
	lst.pop()
	lstk.pop()
	parent = lstk[lstk.length - 1]
	parents = [...lst]
	parents_keys = [...lstk]
	listar()
}

async function upload() {
	loading = true
	const fileInput = document.getElementById("input-file") ;
	const formData = new FormData();
	const file = fileInput.files[0]
	formData.append('file', file);
	formData.append('name', file.name );
	formData.append("parent",parent)
	formData.append("token",token)
	console.log("FORM UP",formData)
	const options = {
		method: 'POST',
		body: formData,
	};

	fetch(`${api}/upload`, options).then(r=>{
		console.log("FETCH R",r)
	}).finally(()=>{
		sel_file=null
		listar()
	} )
}

async function listar() {
	console.log("LISTAR PARENT",parent)
	loading = true
	const json = {
		parent : parent,
		token : token
	}
	const options = {
		method: 'POST',
		body: JSON.stringify(json),
		headers: {
			'Accept': 'application/json',
			'Content-Type': 'application/json'
		},
	};
	fetch(`${api}/listar`, options).then(r=>r.json()).then(r=>{
		console.log("LISTAR",r)
		lista = [...r]
	}).finally(()=>loading=false)
}

async function novapasta() {
	loading = true
	const name = prompt("Nome da pasta","Nova pasta")
	if (name=="") {
		alert("Digite algo")
		return ;
	}
	const json = {
		parent : parent,
		token : token,
		path : path,
		name : name
	}
	const options = {
		method: 'POST',
		body: JSON.stringify(json),
		headers: {
			'Accept': 'application/json',
			'Content-Type': 'application/json'
		},
	};
	fetch(`${api}/novapasta`, options).then(r=>r.json())
	.finally(()=>listar())
}

async function renomear(e) {
	const itm = e.detail
	console.log(e.detail)
	const novo = prompt("Digite o nome do arquivo",itm.name)
	loading = true
	const json = {
		key : itm.key,
		name : novo,
		token : token
	}
	const options = {
		method: 'POST',
		body: JSON.stringify(json),
		headers: {
			'Accept': 'application/json',
			'Content-Type': 'application/json'
		},
	};
	fetch(`${api}/renomear`, options).then(r=>r.json())
	.finally(()=>listar())
}

async function deletar(e) {
	const itm = e.detail
	console.log(e.detail)
	loading = true
	const json = {
		key : itm.key,
		token : token
	}
	const options = {
		method: 'POST',
		body: JSON.stringify(json),
		headers: {
			'Accept': 'application/json',
			'Content-Type': 'application/json'
		},
	};
	fetch(`${api}/deletar`, options).then(r=>r.json())
	.finally(()=>listar())
}

async function download(e) {
	const itm = e.detail
	console.log(e.detail)
	const url = `${api}/download/${token}/${itm.url}`
	window.open(url,"__blank")
}


const click = e => {
	const itm = e.detail
	if (itm.dir==true){
		parents_keys = [...parents_keys, itm.key]
		parents = [...parents, itm.name]
		parent = itm.key
		//pasta_atual = itm.name
		listar()
	}

}

$: {
	if (sel_file!=null) {
		upload()
	}
}


let q = ""

const keyup = e => {
	if (e.keyCode==27) {
		q = ""
		resultados = []
		return ;
	} else if (e.keyCode==13) {
		procurar()
	}
}

let resultados = []

async function procurar() {
	loading = true
	const json = {
		q : q,
		token : token
	}
	const options = {
		method: 'POST',
		body: JSON.stringify(json),
		headers: {
			'Accept': 'application/json',
			'Content-Type': 'application/json'
		},
	};
	fetch(`${api}/procurar`, options).then(r=>r.json()).then(r=>{
		console.log("procurar",r)
		resultados = [...r]
		if (resultados.length==0) {
			alert("Nenhum resultado para essa pesquisa")
		}
	}).finally(()=>loading=false)
}


</script>
<div class="container">
	{#if token!=""}
	<div class="d-flex w-100 mt-3 justify-content-between">
		<h3 on:click={()=>mostrar=!mostrar}>KimDrive - {name}</h3>
		<div class="d-flex align-items-center">
			<div class="input-group me-3">
				<span class="input-group-text" id="basic-addon1">
					<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"></circle><line x1="21" y1="21" x2="16.65" y2="16.65"></line></svg>
				</span>
				<input on:keyup={keyup} bind:value={q} type="text" class="form-control" placeholder="Procurar">
			  </div>
			<button on:click={novapasta} class="btn btn-sm btn-outline-primary me-3">
				<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path><line x1="12" y1="11" x2="12" y2="17"></line><line x1="9" y1="14" x2="15" y2="14"></line></svg>
			</button>
			<form method="POST" enctype="multipart/form-data">
				<label class="btn btn-sm btn-outline-primary me-3">
					<input id="input-file" name="file" type="file" class="d-none" bind:value={sel_file}/>
					<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21.2 15c.7-1.2 1-2.5.7-3.9-.6-2-2.4-3.5-4.4-3.5h-1.2c-.7-3-3.2-5.2-6.2-5.6-3-.3-5.9 1.3-7.3 4-1.2 2.5-1 6.5.5 8.8m8.7-1.6V21"/><path d="M16 16l-4-4-4 4"/></svg>
				</label>
			</form>
			
			<button on:click={()=>location.reload()} class="btn btn-sm btn-primary">Sair</button>
		</div>
	</div>

	<div class="d-flex mt-5 mb-3 align-items-center">
		{#if resultados.length==0}
			<button on:click={voltar} class="btn me-3 btn-sm btn-outline-primary" 
			class:d-none={parents.length==1}
			>
				<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M19 12H6M12 5l-7 7 7 7"/></svg>
			</button>
			<h5>
				{path}
			</h5>
		{:else}
			<h5>Resultado(s): {resultados.length}</h5>
			<button on:click={()=>resultados=[]} class="btn btn-outline-primary ms-4">Fechar</button>
		{/if}
	</div>
	
	
	{#if loading}
	Carregando...
	{:else}
	{#if resultados.length==0}
		{#if lista.length==0}
			<p>Nada pra mostrar aqui</p>
		{/if}
		{#each lista as itm }
			{#if itm.dir==true}
			<Item 
				on:click={click} 
				item={itm} 
				on:renomear={renomear}  
				on:deletar={deletar}  
			/>
			{/if}
		{/each}
		{#each lista as itm }
			{#if itm.dir==false}
			<Item 
				on:click={click} 
				item={itm} 
				on:renomear={renomear}  
				on:deletar={deletar}  
				on:download={download}
			/>
			{/if}
		{/each}
	{:else}
	{#each resultados as itm }
			{#if itm.dir==true}
			<Item 
				on:click={click} 
				item={itm} 
				on:renomear={renomear}  
				on:deletar={deletar}
				path
			/>
			{/if}
		{/each}
		{#each resultados as itm }
			{#if itm.dir==false}
			<Item 
				on:click={click} 
				item={itm} 
				on:renomear={renomear}  
				on:deletar={deletar}  
				on:download={download}
				path
			/>
			{/if}
		{/each}

	
	{/if}

	{#if mostrar}
	<pre>{JSON.stringify(lista,null,1)}</pre>	
	<pre>{JSON.stringify(resultados,null,1)}</pre>
	<button on:click={()=>resultados=[]}>Sair dos resultados</button>
	{/if}
	{/if}
	
{:else}
<div class="login-form">
	{#if signin==true}
		<h1>KimDrive</h1>
		<h6>Cansado do Google Drive e Dropbox? Não?<br>Faça sua conta assim mesmo, não custa nada.</h6>
		<fieldset>
		  <div class="form-group">
			<label for="input-username" class="form-label mt-4">Username</label>
			<input bind:value={username} type="text" class="form-control" id="input-username" aria-describedby="emailHelp" placeholder="">

		  </div>
		  <div class="form-group">
			<label for="input-password" class="form-label mt-4">Password</label>
			<input bind:value={password} type="password" class="form-control" id="input-password" aria-describedby="emailHelp" placeholder="">
		  </div>
		  <button on:click={entrar} class="btn btn-info w-100 mt-4" class:disabled={loading}>
			{loading ? "Aguarde" : "Entrar"}
		</button>
		  <button on:click={()=>signin=false} class="btn btn-link w-100">Não tem cadastro? Clique aqui</button>
		</fieldset>
	{:else if signin==false}
	<h1>KimDrive</h1>
		<h6>Novo cadastro</h6>
		<fieldset>
			<div class="form-group">
				<label for="input-su-name" class="form-label mt-4">Nome</label>
				<input bind:value={signup_name} type="text" class="form-control" id="input-su-name" aria-describedby="emailHelp" placeholder="">
		  </div>
		  <div class="form-group">
			<label for="input-su-username" class="form-label mt-4">Username</label>
			<input bind:value={signup_username} type="text" class="form-control" id="input-su-username" aria-describedby="emailHelp" placeholder="">

		  </div>
		  <div class="form-group">
			<label for="input-su-password" class="form-label mt-4">Password</label>
			<input bind:value={signup_password} type="password" class="form-control" id="input-su-password" aria-describedby="emailHelp" placeholder="">
		  </div>
		  <div class="form-group">
			<label for="input-su-password-2" class="form-label mt-4">Password Confirm</label>
			<input bind:value={signup_password2} type="password" class="form-control" id="input-su-password-2" aria-describedby="emailHelp" placeholder="">
		  </div>
		  <button on:click={signup} class="btn btn-info w-100 mt-4" class:disabled={loading}>
			{loading ? "Aguarde..." : "Cadastrar"}
		</button>
		  <button on:click={()=>signin=true} class="btn btn-link w-100">Tem cadastro? Então clique aqui.</button>
		</fieldset>
	{/if}
</div>
{/if}
</div>

<style>

.login-form {
	position: absolute;
	left: calc(50% - 150px);
	width: 300px;
	display: grid;
	height: 100%;
	align-items: center;
	align-content: center;
}


</style>