<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
  </div>
</template>

<style lang="scss">
#app {
  font-family: "Raleway", "HelveticaNeue", "Helvetica Neue", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #888;
  line-height: 1.6;
  margin: 0;
}
#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
ul, form {
  margin-bottom: 2.5rem;
}
ul {
  list-style: circle inside;
  padding-left: 0;
  margin-top: 0;
}
form {
  margin: auto;
}
input, textarea, select, fieldset {
  margin-bottom: 1.5rem;
}
fieldset {
  border-radius: 4px;
  padding: 1rem 2rem;
}
label, legend {
  display: block;
  margin-bottom: .5rem;
  font-weight: 600;
}
legend {
  border: 0;
  padding: 0;
}
label {
  cursor: default;
}
input[type="submit"] {
  color: #FFF;
  background-color: #42b983;
  height: 38px;
  padding: 0 30px;
  text-align: center;
  font-size: 11px;
  font-weight: 600;
  line-height: 38px;
  letter-spacing: .1rem;
  text-transform: uppercase;
  border-radius: 4px;
  border: 1px solid #bbb;
  cursor: pointer;
}

th, td {
  padding: 12px 15px;
  text-align: left;
  border-bottom: 1px solid #E1E1E1;
}
th:first-child, td:first-child {
  padding-left: 0;
}


.errors {
  color: #ff5555;
}

/* TODO: A specifier par component */
input[type="text"], input[type="password"], textarea {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}
input[type="text"], input[type="password"], textarea, select {
  height: 38px;
  padding: 6px 10px;
  background-color: #fff;
  border: 1px solid #D1D1D1;
  border-radius: 4px;
  box-shadow: none;
  box-sizing: border-box;
}
</style>
