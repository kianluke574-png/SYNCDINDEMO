app.js
// ---------------- STATE ----------------

const state = {
  currentUser: null,
  users: [],
  posts: [],
  currentView: "home"
};

// ---------------- DOM ----------------

const authScreen = document.querySelector("#auth-screen");
const appScreen = document.querySelector("#app-screen");

const signupBtn = document.querySelector("#signup-btn");
const loginBtn = document.querySelector("#login-btn");
const logoutBtn = document.querySelector("#logout-btn");

const usernameInput = document.querySelector("#username");
const postInput = document.querySelector("#post-input");
const postBtn = document.querySelector("#post-btn");

const feed = document.querySelector("#feed");
const profilePosts = document.querySelector("#profile-posts");
const profileUsername = document.querySelector("#profile-username");

const homeBtn = document.querySelector("#home-btn");
const profileBtn = document.querySelector("#profile-btn");

const homeView = document.querySelector("#home-view");
const profileView = document.querySelector("#profile-view");

// ---------------- INIT ----------------

window.addEventListener("DOMContentLoaded", init);

function init() {
  loadData();
  if (state.currentUser) showApp();
  render();
}

function loadData() {
  state.users = JSON.parse(localStorage.getItem("users")) || [];
  state.posts = JSON.parse(localStorage.getItem("posts")) || [];
  state.currentUser = JSON.parse(localStorage.getItem("currentUser"));
}

function saveData() {
  localStorage.setItem("users", JSON.stringify(state.users));
  localStorage.setItem("posts", JSON.stringify(state.posts));
  localStorage.setItem("currentUser", JSON.stringify(state.currentUser));
}

// ---------------- AUTH ----------------

signupBtn.addEventListener("click", () => {
  const username = usernameInput.value.trim();
  if (!username) return;

  if (state.users.find(u => u.username === username)) {
    alert("Username taken");
    return;
  }

  const user = { id: Date.now(), username };
  state.users.push(user);
  state.currentUser = user;

  saveData();
  showApp();
  render();
});

loginBtn.addEventListener("click", () => {
  const username = usernameInput.value.trim();
  const user = state.users.find(u => u.username === username);
  if (!user) return alert("User not found");

  state.currentUser = user;
  saveData();
  showApp();
  render();
});

logoutBtn.addEventListener("click", () => {
  state.currentUser = null;
  localStorage.removeItem("currentUser");
  appScreen.classList.remove("active");
  authScreen.classList.add("active");
});

// ---------------- ROUTING ----------------

homeBtn.addEventListener("click", () => switchView("home"));
profileBtn.addEventListener("click", () => switchView("profile"));

function switchView(view) {
  state.currentView = view;
  render();
}

// ---------------- POSTS ----------------

postBtn.addEventListener("click", () => {
  const content = postInput.value.trim();
  if (!content) return;

  const post = {
    id: Date.now(),
    userId: state.currentUser.id,
    content,
    likes: [],
    createdAt: new Date().toISOString()
  };

  state.posts.unshift(post);
  postInput.value = "";
  saveData();
  render();
});

function deletePost(postId) {
  state.posts = state.posts.filter(p => p.id !== postId);
  saveData();
  render();
}

function toggleLike(post) {
  const userId = state.currentUser.id;

  if (post.likes.includes(userId)) {
    post.likes = post.likes.filter(id => id !== userId);
  } else {
    post.likes.push(userId);
  }

  saveData();
  render();
}

// ---------------- RENDER ----------------

function render() {
  renderView();
  renderFeed();
  renderProfile();
}

function renderView() {
  homeView.classList.toggle("hidden", state.currentView !== "home");
  profileView.classList.toggle("hidden", state.currentView !== "profile");
}

function renderFeed() {
  feed.innerHTML = "";

  state.posts.forEach(post => {
    const user = state.users.find(u => u.id === post.userId);
    feed.appendChild(buildPost(post, user));
  });
}

function renderProfile() {
  profileUsername.textContent = `@${state.currentUser.username}`;
  profilePosts.innerHTML = "";

  const myPosts = state.posts.filter(
    p => p.userId === state.currentUser.id
  );

  myPosts.forEach(post => {
    profilePosts.appendChild(buildPost(post, state.currentUser));
  });
}

function buildPost(post, user) {
  const el = document.createElement("div");
  el.className = "post";

  const isOwner = post.userId === state.currentUser.id;
  const liked = post.likes.includes(state.currentUser.id);

  el.innerHTML = `
    <div class="post-header">
      <strong>@${user.username}</strong>
      <small>${new Date(post.createdAt).toLocaleString()}</small>
    </div>
    <div class="post-content">${post.content}</div>
    <div class="post-actions">
      <button class="like-btn">
        ❤️ ${post.likes.length} ${liked ? "(Liked)" : ""}
      </button>
      ${isOwner ? `<button class="delete-btn">Delete</button>` : ""}
    </div>
  `;

  el.querySelector(".like-btn")
    .addEventListener("click", () => toggleLike(post));

  if (isOwner) {
    el.querySelector(".delete-btn")
      .addEventListener("click", () => deletePost(post.id));
  }

  return el;
}

function showApp() {
  authScreen.classList.remove("active");
  appScreen.classList.add("active");
}