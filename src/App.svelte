<script>
  import FolderFiles from "./lib/FolderFiles.svelte";
  import Folders from "./lib/Folders.svelte";
  import { Router, Route, Link } from "svelte-routing";
  export let url = ""; //This property is necessary declare to avoid ignore the Router
</script>



<Router url="{url}">
  <nav>
     <Link to="/">Home</Link>
     
   </nav>
   <div>
     <Route path="/" component="{Folders}" /> 
     <!--for now the router just support case sensitive,
         one workaround colud be add two time the route
         Example.
        <Route path="About" component="{About}" /> 
     -->
     <Route path="/:id" let:params>
      <FolderFiles params="{params}" />
     </Route>
   </div>
 </Router>

