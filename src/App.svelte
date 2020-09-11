<svelte:head>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
</svelte:head>

<script>
	import { Col, Container, Row, Card, CardHeader, CardTitle, CardText, CardBody, CardFooter, Input, Form, FormGroup, Label, ListGroup, ListGroupItem } from "sveltestrap";
	import {onMount} from "svelte";
	let counter = 0;
	let items = [];
	let completedItems = [];

	let todoInput;

	async function addTodo(event) {
		event.preventDefault();
		if (todoInput.length === 0) {
			alert("Debe ingresar una tarea");
			return;
		}

		const formData = new FormData();
		formData.append('task',todoInput);

		await fetch(`http://127.0.0.1:5001/api/todos`, {
			method: 'POST',
			body: formData
		})
		.then(data => data.json())
		.then(response => {
			items = response;
			//Todo check errors
		});

		todoInput = "";
	}

	async function deleteTodo(todo) {

		await fetch(`http://127.0.0.1:5001/api/todos/` + todo[0], {
			method: 'DELETE'
		})
				.then(data => data.json())
				.then(response => {
					items = response;
					//Todo check errors
				});
	}

	async function updateTodo(event,todo) {

		const formData = new FormData();
		formData.append('completed',event.target.checked ? 1 : 0);

		await fetch(`http://127.0.0.1:5001/api/todos/` + todo[0], {
			method: 'PUT',
			body: formData
		})
				.then(data => data.json())
				.then(response => {
					console.log(response);
					//Todo check errors
				});
	}


	onMount(async () => {
		await fetch(`http://127.0.0.1:5001/api/todos`,{
		})
				.then(data => data.json())
				.then(response => {
					items = response;
					//Todo check errors
				});
	})

</script>

<style>
	.delete_todo {
		color: red;
		font-weight: bold;
	}
</style>

<Container>
	<Row>
		<Col sm="12" md={{ size: 6, offset: 3 }}>
			<Card>
				<CardHeader>
					<CardTitle>
						ToDo List
					</CardTitle>
				</CardHeader>
				<CardBody>
					<Form on:submit={event => addTodo(event)}>
						<Input bind:value={todoInput} placeholder="Nueva Tarea" autofocus />
					</Form>
				</CardBody>
			</Card>
			<Card>
				<CardBody>
					<ListGroup>
						{#if items.length === 0}
							<CardText>
								No hay ninguna tarea cargada.
							</CardText>
						{:else}
							{#each items as item}
								<ListGroupItem>
									<Row>
										<Col xs="9">{item[1]}</Col>
										<Col xs="2">
											<input
													on:change={event => updateTodo(event,item)}
													value="{item[0]}"
													type="checkbox"
													bind:checked={item[2]}
											/>
										</Col>
										<Col>
											<a on:click={_ => deleteTodo(item)} class="delete_todo" href="#">X</a>
										</Col>
									</Row>
								</ListGroupItem>
							{/each}
						{/if}
					</ListGroup>
					<CardFooter>
						{#if completedItems.length === 0}
							No hay tareas campletadas
						{:else if completedItems.length === 1}
							1 Tarea Completada
						{:else}
							{completedItems.length} Tareas Completadas
						{/if}
					</CardFooter>
				</CardBody>
			</Card>
		</Col>
	</Row>
</Container>
