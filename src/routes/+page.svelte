<script>
  import { Ollama } from "ollama";
  import { browser } from "$app/environment";

  let input = "";

  const hostIp = browser && localStorage.getItem("hostIp");
  const model = browser && localStorage.getItem("model");
  const ollama = new Ollama({ host: `http://${hostIp}:11434` });

  async function chat(content) {
    const response = await ollama.chat({
      model,
      messages: [{ role: "user", content }],
    });
    return response;
  }

  async function handleClick() {
    const r = await chat(input);

    const messagesDiv = document.querySelector(".messages");

    const userMessageElement = document.createElement("div");
    userMessageElement.className = "user-message";
    userMessageElement.textContent = input;
    messagesDiv.appendChild(userMessageElement);

    const messageElement = document.createElement("div");
    messageElement.textContent = r.message.content;
    messagesDiv.appendChild(messageElement);

    const parser = new DOMParser();
    const doc = parser.parseFromString(r.message.content, "text/html");
    const thinkElement = doc.querySelector("think");
    messageElement.textContent = doc.body.innerHTML;

    input = undefined;
  }
</script>

<h1>alpaca</h1>
<div class="messages"></div>
<div class="bottom">
  <textarea bind:value={input}></textarea>
  <button onclick={handleClick}>Submit</button>
</div>

<style>
  @import "../styles/sakura.css";

  * {
    box-sizing: border-box;
  }

  .messages {
    margin-bottom: 100px;
  }
  .bottom {
    position: fixed;
    bottom: 0;
    width: 38em;
    background: white;
    padding: 8px;
    display: flex;
    justify-content: space-between;
    gap: 8px;
  }

  textarea {
    font-family: inherit;
  }
</style>
