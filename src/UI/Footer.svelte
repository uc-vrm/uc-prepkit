<style>
    .conferm-box{
        width:300px;
        padding: 20px;
        position: fixed;
        top:50%;
        left:50%;
        background-color: rgb(207, 217, 250);
        border-radius: 10px;
        text-align: center;
        transform: translate(-50%,-50%);
        display: none;
    }
    .conferm-box button{
        padding: 20px 10px;
        width: 100px;
        margin-left: 30px;
    }
    footer{
        position: fixed;
        bottom: 0px;
        right: 10px;
        width: 97%;
        padding: 5px 0px;   
        padding-left: 22px;
        background-color: rgb(237, 247, 248);
        z-index: 10 ;
        border-top: 2px solid rgba(0, 0, 0, 0.3);
    }
    footer div{
        float: right;
        width: 70%;
    }
    footer div div,button{
        float: left;
        padding: 10px 0px;
        margin: 0px 10px;
        width: 14%;
        text-align: center;
    }
    button{
        background-color: rgb(221, 236, 238);
    }
    .fetch-ques{
        margin-bottom: 20px;
    }
    span{
        display: inline-block;
    }
    /* sitdebar starts */
    .sidebar{
        position: fixed;
        height: 100%;
        width: 300px;
        background-color: rgb(224, 230, 240);
        top: 0;
        left: 0;
        z-index: 20;
        transition: all 1s;
        transform: translateX(-300px);
        overflow-y: scroll;
        overflow-x: hidden;
    }
    .close{
        padding: 4px 8px;
        position: fixed;
        right: 0;
        top: 10px;
    }
    .side-questions{
        padding: 20px 15px;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
        cursor: pointer;
    }
    .side-questions:hover{
        background-color: rosybrown;
    }
    .sidebar div:first-of-type{
        margin-top: 50px;
    }

    /* result style */
    
    .result-box>div:first-of-type{
        border-top: 1px solid black;
    }
    .result{
        width: 100%;
        margin: 0;
        border: 1px solid black;
        height: 37px;
        border-top: 0px; 
    }
    .result div{
        float: left;
        padding: 8px;
        margin: 0;
    }
    .result .result-answers{
        width: 14%;
    }
    .result-question{
        width:80%;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
        cursor: pointer;
        border-right: 1px solid black;
    }
    .result-question:hover{
        background-color: rosybrown;
    }
    /* result style end */
    
</style>
<script>
    import * as myjson from '../question.json';
    export let isTestBegin = true;
    let second=59,hr=0,minute=10;
    let startTimer;
    let arr = [];
    let contentArr = [];
    let answers = [];
    let i = 0;
    let j = 0;
    let attemptedQuestions = 0;
    //fetchItems starts
    arr = myjson["default"];
    let contentId = arr[i]["content_id"];
    contentArr = JSON.parse(arr[i]["content_text"]);
    answers = contentArr["answers"];
    let lengthOfArr = arr.length;
    //fetchItem ends
    if(isTestBegin){
        setInterval(()=>{
            second--;
            if(second<0){
                second = 59;
                minute--;
            }
            if(minute<0){
                minute = 59;
                hr--;
            }
            if(minute==0 && second==0){
                endTest(1);
            }

        },1000);
    }
    const openList = () => {
        let sidebar = document.getElementById("sidebar");
        sidebar.style.transform = "translateX(0px)";
    }
    const nextItem = () =>{
        let chk = document.getElementsByName("chk");
        
        // check attempted questions
        for(let k=0; k<chk.length; k++){
            if(chk[k].checked==true){
                console.log(answers[k]["id"]);
                attemptedQuestions++;
                // add color to attempted questions
                let listOfClass = document.getElementsByClassName("side-questions");
                for(let j=0; j<listOfClass.length; j++){
                    if(j == i){
                        listOfClass[j].classList.add("add-color");
                    }
                }
                // end attempted questions
            }
        }
        // end of checking attempted questions

        if(i >= lengthOfArr-1){
        }else{
            i++;
        }
        contentArr = JSON.parse(arr[i]["content_text"]);
        answers = contentArr["answers"];
        contentId = arr[i]["content_id"];

        chk = document.getElementsByName("chk");
        for(let k=0; k<chk.length; k++){
            chk[k].checked = false;
        }
    }

    const prevItem = () =>{
        if(i<=0){
        }else{
            i--;
        }
        contentArr = JSON.parse(arr[i]["content_text"]);
        answers = contentArr["answers"];
        contentId = arr[i]["content_id"];

        let chk = document.getElementsByName("chk");
        for(let k=0; k<chk.length; k++){
            chk[k].checked = false;
        }
    }
    
    const endTestConferm = () =>{
        document.getElementById("conferm-box").style.display = "block";    
    }

    function endTest(e){
        if(e == 1){
            isTestBegin = false;
        }
        document.getElementById("conferm-box").style.display = "none";
    }
   
    // sidebar starts
    const closeList = () =>{
        let sidebar = document.getElementById("sidebar");
        sidebar.style.transform = "translateX(-300px)";
    }
    function jump(j){
        i=j;
        contentArr = JSON.parse(arr[i]["content_text"]);
        answers = contentArr["answers"];
        contentId = arr[i]["content_id"];
    }
</script>

<div class="conferm-box" id="conferm-box">
    <h2>Are you sure</h2>
    <button on:click="{()=>endTest(1)}">OK</button>
    <button on:click="{()=>endTest(0)}">Cancel</button>
</div>

<div class="sidebar" name="sidebar" id="sidebar">
    <button class="close" on:click="{closeList}">x</button>
    {#each arr as item,j}
        <div class="side-questions" on:click="{()=>jump(j)}"><b>{j+1}.</b>{item["snippet"]}</div>
    {/each}
</div>

{#if isTestBegin}
<!-- fetch items -->
<div class="fetch-ques"><b>{i+1}. </b>{contentArr["question"]}</div>  

{#each answers as item}
    <span class="check"><input type="radio" name="chk" id="{contentId}"/></span>
    <span class="ans"><label for="chk">{@html item["answer"]}</label></span>
    <br>
{/each}
<!-- end fetch items -->
<footer>
    <div>
        <div>{("0"+hr).slice(-2)}:{("0"+minute).slice(-2)}:{("0"+second).slice(-2)}</div>
        <button on:click="{openList}">List</button>
        <button on:click="{prevItem}">Previous</button>
        <div>{i+1} 0f {lengthOfArr}</div>
        <button on:click="{nextItem}">Next</button>
        <button on:click="{endTestConferm}">End Test</button>
    </div>
</footer>
{:else}
<!-- <h1>Attempted Questions:{attemptedQuestions}</h1> -->
    <div class="result-box">
        {#each arr as item,j}
            <div class="result">    
                <div class="result-question">{j+1}.{item["snippet"]}</div>
                <div class="result-answers">correct</div>
            </div>
        {/each}
    </div>
{/if}