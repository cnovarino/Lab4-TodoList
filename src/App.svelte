<svelte:head>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
</svelte:head>

<script>
	import { Col, Container, Row, Card, CardHeader, CardTitle, CardText, CardBody, CardFooter, Input, Form, FormGroup, Label, ListGroup, ListGroupItem } from "sveltestrap";
	let counter = 0;
	let items = [];
	let completedItems = [];

	let todoInput;

	function addTodo(event) {
		event.preventDefault();
		if(todoInput.length === 0){
			alert("Debe ingresar una tarea");
			return;
		}
		items = [...items, { id: ++counter, text: todoInput, completed: false}];
		todoInput = "";
	}

</script>

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
										<Col xs="10">{item.text}</Col>
										<Col xs="2">
											<input bind:group={completedItems} value="{item.id}" type="checkbox" />
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
