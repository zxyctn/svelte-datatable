<script>
  import {
    Icon,
    Styles,
    Button,
    Modal,
    ModalBody,
    ModalFooter,
    ModalHeader,
    Form,
    FormGroup,
    Input,
  } from "sveltestrap";

  let open = false;
  const toggle = () => (open = !open);

  export let row;
</script>

<button class="btn btn-sm btn-outline-dark" on:click={toggle}>
  <Icon name="pencil-fill" />
</button>

<button class="btn btn-sm btn-outline-dark">
  <Icon name="people-fill" />
</button>

<button class="btn btn-sm btn-outline-dark">
  <Icon name="trash-fill" />
</button>

<Styles />

<div>
  <Modal isOpen={open} {toggle}>
    <ModalHeader {toggle}>Edit Group: {row.name.value}</ModalHeader>
    <ModalBody>
      <Form>
        <FormGroup floating label="Id">
          <Input value={row.id.value} disabled />
        </FormGroup>

        <FormGroup floating label="Name">
          <Input value={row.name.value} />
        </FormGroup>

        <FormGroup floating label="Description">
          <Input value={row.description.value} />
        </FormGroup>
      </Form>
    </ModalBody>
    <ModalFooter>
      <Button color="primary" on:click={toggle}>Save</Button>
      <Button color="secondary" on:click={toggle}>Cancel</Button>
    </ModalFooter>
  </Modal>
</div>
