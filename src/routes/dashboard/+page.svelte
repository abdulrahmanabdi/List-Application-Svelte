<script>
   import {authHandlers, authStore} from "../../store/store"
   import {getDoc, doc, setDoc} from 'firebase/firestore'
   import { db } from '../../lib/firebase/firebase';
   import { onMount } from 'svelte';

    let todoList = []
    let currTodo = "";
    let error = false;

     

    authStore.subscribe((curr)=>{
        console.log(curr)
        todoList = curr.todos
    });

    function addTodo(){
        error = false;
        if(!currTodo){
            error = true;
        }
        todoList = [...todoList, currTodo]
        currTodo = "";
    }
    function editTodo(index){
        console.log(index);
        let newTodoList = [...todoList].filter((val,i)=>{
            console.log(i, index, i!==index);
            return i!= index;
        });
        currTodo = todoList[index];
        todoList = newTodoList;
    }
    function removeTodo(index){
        console.log(index);
        let newTodoList = [...todoList].filter((val,i)=>{
            console.log(i, index, i!==index);
            return i!= index;
        });
        
        todoList = newTodoList;
    }
    async function save() {
  try {
    
    const userRef = doc(db, 'users', $authStore.user.uid);

    
    await setDoc(
      userRef,
      {
        todos: todoList,
      },
      { merge: true }
    );

    console.log('Data saved successfully!');
  } catch (err) {
    console.error('Error! Could not save:', err);
  }
}
</script>
{#if !$authStore.loading}
<div class="mainContainer">
    <div class="headerContainer">
            <h1>List</h1>
            <div class="headerBtns">
            <button on:click={save}><i class="ri-save-2-line"></i>Save</button>
            <button on:click={authHandlers.logout}><i class="ri-logout-box-line"></i>Log out</button>
        </div>
    </div>

<main>
    {#if todoList.length===0}
    <p>YEY! You have nothing to do.</p>
    {/if}
    {#each todoList as todo, index}
    <div class="todo"> 
        <p>
            {index +1}. {todo}
        </p>
        <div class="actions">
            <i on:click={()=>
             editTodo(index)
             } 
             on:keydown={()=>{}} 
             class="ri-edit-2-line"></i>
            <i on:click={()=>
                removeTodo(index)
                } 
                on:keydown={()=>{}} 
                class="ri-delete-bin-6-line"></i>
        </div>
    </div>
    {/each}
</main>
<div class="enterTodo">
<!-- <div class="enterTodo" + (error ? "errorBorder": "")> -->

    <input bind:value = {currTodo} type="text" placeholder="Enter Todo"/>
    <button on:click={addTodo}>ADD</button>
</div>
</div>
{/if}
<style>
    .mainContainer{
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        gap: 24px;
        padding: 24px;
        width: 100%;
        max-width: 1000px;
        margin: 0 auto;
    }
    .headerContainer{
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .headerContainer button{
        background: #003c5b;
        color: white;
        padding: 10px 18px;
        border: none;
        border-radius: 4px;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 8px;
        cursor: pointer;
    }
    .headerContainer button i{
        font-size: 1.2rem;
    }
    .headerContainer button:hover{
        opacity: 0.7;
    }
    .headerBtns{
        display: flex;
        align-items: center;
        gap: 14px;
    }
    
    main{
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;
    }
    .todo{
        border-left: 1px solid cyan;
        padding: 8px 14px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .actions{
        display: flex;
        align-items: center;
        gap: 14px;
        font-size: 1.3rem;
    }
    .actions i{
        cursor: pointer;
        font-size: 1.6rem;
    }
    .actions i:hover{
        color: coral;
    }
    .enterTodo{
        display: flex;
        align-items: stretch;
        border: 1px solid #0891b2;
        border-radius: 5px;
        overflow: hidden;
    }
    .enterTodo input{
        background: transparent;
        border: none;
        padding: 14px;
        color: white;
        flex: 1;
    }
    .enterTodo input:focus{
        outline: none;
    }
    .enterTodo button{
        padding: 0 20px;
        background: #003c5b;
        border: none;
        color: cyan;
        font-weight: 600;
        cursor: pointer;
    }
    .enterTodo button:hover{
        background: transparent;
    }
</style>