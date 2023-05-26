<script>
  let applicants = [    { names: ['book 8', 'book 7', 'book3'] },
    { names: ['book 3', 'book 5'] },
    { names: ['book 6', 'book 1', 'book 2'] }
  ];

  let hoveringOverApplicant = null;

  function dragStart(event, applicantIndex, nameIndex) {
    const data = { applicantIndex, nameIndex };
    event.dataTransfer.setData('text/plain', JSON.stringify(data));
  }

  function drop(event, applicantIndex, nameIndex = null) {
    event.preventDefault();
    const json = event.dataTransfer.getData('text/plain');
    const data = JSON.parse(json);

    if (nameIndex !== null) {
      // Reordering names within the same applicant
      const [name] = applicants[data.applicantIndex].names.splice(data.nameIndex, 1);
      applicants[applicantIndex].names.splice(nameIndex, 0, name);
    } else {
      // Moving name to a different applicant
      const [name] = applicants[data.applicantIndex].names.splice(data.nameIndex, 1);
      applicants[applicantIndex].names.push(name);
    }

    hoveringOverApplicant = null;
    applicants = [...applicants]; // Make a copy to trigger reactivity
  }

  function deleteName(applicantIndex, nameIndex) {
    applicants[applicantIndex].names.splice(nameIndex, 1);
    applicants = [...applicants]; // Make a copy to trigger reactivity
  }

  function editName(applicantIndex, nameIndex, newName) {
    applicants[applicantIndex].names[nameIndex] = newName;
    applicants = [...applicants]; // Make a copy to trigger reactivity
  }

  function addName(applicantIndex) {
    const newName = prompt('Enter a new name:');
    if (newName) {
      applicants[applicantIndex].names.push(newName);
      applicants = [...applicants]; // Make a copy to trigger reactivity
    }
  }
</script>

<div class="applicants">
  {#each applicants as applicant, i}
    <div
      class="applicant"
      on:dragover={(event) => {
        event.preventDefault();
        hoveringOverApplicant = i;
      }}
      on:drop={(event) => drop(event, i, null)}
      on:dragleave={() => hoveringOverApplicant = null}
      style="background-color: {i === hoveringOverApplicant ? 'lightgray' : 'rgb(205, 209, 228)'};"
    >
      <h2 style="text-align: center; "> Stage{i+1}
	  <i class="bi bi-plus" on:click={() => addName(i)}> + </i>
	  <ul>
        {#each applicant.names as name, j}
          <li
            class="name"
            draggable="true"
            on:dragstart={(event) => dragStart(event, i, j)}
            on:dragover={(event) => {
              event.preventDefault();
              hoveringOverApplicant = i;
            }}
            on:drop={(event) => drop(event, i, j)}
          >
            <div style="display: flex; align-items: center;">
              <span style="font-size:20px">{name}</span>
              <div style="margin-left: 50px;">
                <button on:click={() => deleteName(i, j)} class="btn btn-danger">Delete</button>
				<!-- <button on:click={() => deleteName(i, j)} type="button" class="close" aria-label="Close" style="background-color:red">
					<span aria-hidden="true">&times;</span>
				  </button> -->
				  <!-- <button><i class="bi bi-plus" on:click={() => deleteName(i, j)} style="color:red"> X </i></button> -->
                <button on:click={() => editName(i, j, prompt('Enter a new name:'))} class="btn btn-primary">Edit</button>
				
              </div>
            </div>
          </li>
        {/each}
      </ul>
  </div>
  {/each}
</div>




<style>
  .applicants {
      
    display: flex;
    justify-content: space-between;
	margin-top: 50px;
	/* padding:100px */
	
  }

  .applicant {
     
    width: 400px;
    padding: 20px;
    border: none;
    margin: 10px;
	border-radius: 25px;
	
  }

  .name {
    background-color:rgb(205, 209, 228);
    cursor: move;
    margin: 5px;
    padding: 5px;
    border: 1px solid white;
    display: flex;
    justify-content: space-between;
	border-radius: 5px;
	
  }
</style>
