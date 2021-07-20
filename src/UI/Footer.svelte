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
        position: sticky;
        top: 10px;
        left: 300px;
    }
    .side-questions{
        padding: 20px 15px;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
        cursor: pointer;
    }
    .side-questions:hover{
        background-color: rgb(214, 197, 197);
    }
    .sidebar div:first-of-type{
        margin-top: 50px;
    }

    /* result style */
    .showScore{
        display: block;
        width: 100%;
        height: 37px;
        border: 1px solid black;
    }
    .showScore div{
        float: left;
        width: 18.5%;
        text-align: center;
        padding: 8px;
        border-right: 1px solid black; 
    }
    .showScore div:last-of-type{
        border-right: 0px;
    }
    .result-box{
        margin-top: 0px;
    }
    .result-box>div.result:first-of-type{
        border-top: 1px solid black;
    }
    .result{
        width: 100%;
        margin-top: 0;
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
        background-color: rgb(252, 244, 244);
    }
    .open-result{
        position: fixed;
        bottom: 10px;
        right: 10px;
        padding: 10px;
    }
    .explain-result{
        margin-top: 20px;
    }
    /* result style end */
    
</style>
<script>
    import * as myjson from '../question.json';
    import attemptedAnswers from '../store/storedata';
    export let isTestBegin = true;
    let second=59,hr=0,minute=9;
    let startTimer;
    let arr = [];
    let contentArr = [];
    let answers = [];
    let i = 0;
    let j = 0;
    let n;
    let storeData = [];
    let attemptedQuestions = 0;
    let correct = 0;
    let incorrect = 0;
    let isResult = false;
    let ap = 0;
    //fetchItems starts
    arr = myjson["default"];
    let contentId = arr[i]["content_id"];
    contentArr = JSON.parse(arr[i]["content_text"]);
    answers = contentArr["answers"];
    let lengthOfArr = arr.length;
    //fetchItem ends
    attemptedAnswers.subscribe(data => {
        // console.log(data);
        storeData = data;
    });
    // explanation of result
    let explainId;
    let explainArr = [];
    let explainAnswers = [];
    let fullstr = "";
    const openResult = () => {
        isResult = true;
    }
    // timer
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

    //storing and updating data to store
    const saveData = () => {
         // check attempted questions
         let chk = document.getElementsByName("chk");
         for(let k=0; k<chk.length; k++){
            if(chk[k].checked==true){
                // console.log(answers[k]["id"]);
                let answerId = answers[k]["id"];
                let isAlreadyExist = false;
                let data = {content_id:contentId,answer_id:answerId};
                attemptedAnswers.update(currentData => {
                    currentData.forEach((oldData,index) => {
                        if(oldData["content_id"]==contentId){
                            currentData[index].answer_id = answerId;
                            isAlreadyExist = true;
                            // console.log("data matched");
                        }
                    });
                    if(isAlreadyExist == true){
                        return [...currentData];
                    }else{
                        ap++;
                        return [...currentData,data];
                    }
                });

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
    }

    //fetch record from store
    const explain = (j) => {
        isResult = false;
        explainId = arr[j]["content_id"];
        explainArr = JSON.parse(arr[j]["content_text"]);
        explainAnswers = explainArr["answers"]; 
        let correctOption = 0;
        let c = 0;
        let cp = false;
        explainAnswers.forEach(function(option){
            if(option["is_correct"]==1){
                correctOption = c;
                cp = true;
            }else{
                c++;
            }
        });
        fullstr = "";
        let correctOptions = ["A","B","C","D"];
        let optionIndex = explainArr["explanation"].indexOf("option");
        if(optionIndex < 0){
            optionIndex = explainArr["explanation"].indexOf("Answer");
        }
        let firststr = explainArr["explanation"].substring(0,optionIndex+6);
        let secondstr = explainArr["explanation"].substring(optionIndex+6);
        let emptyArr = false;
        while(!emptyArr){
            if(cp==true){
                fullstr = fullstr +" "+ firststr +" "+ correctOptions[correctOption];
                cp = false;
                correctOptions.splice(correctOption,1);
            }else{
                if(optionIndex>0){
                    optionIndex = secondstr.indexOf("option");
                    firststr = secondstr.substring(0,optionIndex+6);
                    secondstr = secondstr.substring(optionIndex+6);
                    fullstr = fullstr +" "+ firststr +" "+ correctOptions[0];
                    correctOptions.shift();
                }else{
                    if(optionIndex<0){
                        emptyArr = true;
                    }else{
                        optionIndex = secondstr.indexOf("Answer");
                        firststr = secondstr.substring(0,optionIndex+6);
                        secondstr = secondstr.substring(optionIndex+6);
                        fullstr = fullstr +" "+ firststr +" "+ correctOptions[0];
                        correctOptions.shift();
                    }
                }
            }    
        }
        fullstr = fullstr +""+secondstr;
        n = j;
        checkExplain();
    }

    const checkExplain = () =>{
        let chkVal = []
        chkVal = document.getElementsByClassName("chkVal");
        console.log(chkVal);
        storeData.forEach((item)=>{
            if(explainId==item["content_id"]){
                let next = 0;
                explainAnswers.forEach((val)=>{
                    if(val["id"]==item["answer_id"]){
                        chkVal[next].checked = true;
                    }else{
                        next++;
                    }
                });
            }
        });
    }

    let checkCorrect = () => {
        arr.forEach((items)=>{
            let item = JSON.parse(items["content_text"]);
            let correctAnswers = item["answers"];
            storeData.forEach((storeItem)=>{
                correctAnswers.forEach((correctAnswer)=>{
                    if(storeItem["content_id"]==items["content_id"] && storeItem["answer_id"]==correctAnswer["id"]){
                        if(correctAnswer["is_correct"]==1){
                            correct++;
                        }else{
                            incorrect++;
                        }
                    }
                });
            });
        });
    }
    //select attempted questions
    const checkAttempted = () => {
        let chk = document.getElementsByName("chk");
         storeData.forEach((item)=>{
            if(contentId==item["content_id"]){
                let next = 0;
                answers.forEach((val)=>{
                    if(val["id"]==item["answer_id"]){
                        chk[next].checked = true;
                    }else{
                        next++;
                    }
                });
            }
        });
    }

    const nextItem = () =>{
        saveData();
        if(i >= lengthOfArr-1){
        }else{
            i++;
        }
        contentArr = JSON.parse(arr[i]["content_text"]);
        answers = contentArr["answers"];
        contentId = arr[i]["content_id"];
        let chk = document.getElementsByName("chk");
        for(let k=0; k<chk.length; k++){
            chk[k].checked = false;
        }
        checkAttempted();
        disableBtn();
    }

    const prevItem = () =>{
        saveData();
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
        checkAttempted();
        disableBtn();
    }

    const disableBtn = () => {
        if(i<=0){
            document.getElementById("prev").disabled = true;
        }else{
            document.getElementById("prev").disabled = false;
        }
        if(i>=lengthOfArr-1){
            document.getElementById("nxt").disabled = true;
        }else if(i<lengthOfArr-1){
            document.getElementById("nxt").disabled = false;
        }
    }
    
    const endTestConferm = () =>{
        document.getElementById("conferm-box").style.display = "block";    
    }

    function endTest(e){
        if(e == 1){
            isTestBegin = false;
            isResult = true;
            saveData();
            checkCorrect();
        }
        document.getElementById("conferm-box").style.display = "none";
    }
   
    // sidebar starts
    const closeList = () =>{
        let sidebar = document.getElementById("sidebar");
        sidebar.style.transform = "translateX(-300px)";
    }
    function jump(j){
        saveData();
        i=j;
        contentArr = JSON.parse(arr[i]["content_text"]);
        answers = contentArr["answers"];
        contentId = arr[i]["content_id"];
        let chk = document.getElementsByName("chk");
        for(let k=0; k<chk.length; k++){
            chk[k].checked = false;
        }
        checkAttempted();
    }
    
</script>

{#if isTestBegin}
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
        <button on:click="{prevItem}" id="prev">Previous</button>
        <div>{i+1} 0f {lengthOfArr}</div>
        <button on:click="{nextItem}" id="nxt">Next</button>
        <button on:click="{endTestConferm}">End Test</button>
    </div>
</footer>
{:else if isResult}
    <div class="showScore">
        <div>Attempted:{ap}</div>
        <div>Unattempted:{lengthOfArr-ap}</div>
        <div>Correct:{correct}</div>
        <div>Incorrect:{incorrect}</div>
        <div>Percentage:{parseFloat((correct*100)/lengthOfArr).toFixed(2)}%</div>
    </div>
    <div class="result-box">
        {#each arr as item,j}
            <div class="result" on:click="{()=>explain(j)}">    
                <div class="result-question">{j+1}.{item["snippet"]}</div>
                {#each storeData as answerData}
                    {#each (JSON.parse(item["content_text"]))["answers"] as answerCorrect}
                        {#if answerData["content_id"]==item["content_id"] && answerData["answer_id"]==answerCorrect["id"]}
                            {#if answerCorrect["is_correct"] == 1}
                                <div class="result-answers">correct</div>
                            {:else}
                                <div class="result-answers">Incorrect</div>
                            {/if} 
                        {/if}
                    {/each}
                {/each}
            </div>
        {/each}
    </div>
{:else}
    <div class="fetch-ques"><b>{n+1}. </b>{explainArr["question"]}</div>  
    {#each explainAnswers as item}
        <span class="check"><input type="radio" name="chk" class="chkVal"/></span>
        <span class="ans"><label for="chk">{@html item["answer"]}</label></span>
        <br>
    {/each}
    <div class="explain-result">
        {@html fullstr}
    </div>
    <button class="open-result" on:click="{openResult}">Open Result</button>
{/if}