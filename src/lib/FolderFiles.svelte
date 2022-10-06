<script>
// @ts-nocheck

    import { Link } from "svelte-routing";
    import { onMount } from 'svelte';
    import PocketBase from 'pocketbase';

    const client = new PocketBase('http://142.93.119.251');


    // fetch a paginated records list
    // @ts-ignore
    let folders=[];
    let files=[];

    export let params;
    
    
    onMount(async () => {
        folders=[]
        files=[]

        const folderResultList = await client.records.getList('child_folders', 1, 50, {
            filter: `parent = \"${params.id}\"`,
            'expand': 'child',
        });

        console.log(folderResultList)

        const fileResultList = await client.records.getList('files', 1, 50, {
            filter: `folder = \"${params.id}\"`,
        });

        if (folderResultList) {
            folders=folderResultList.items
        }
        if (fileResultList) {
            files=fileResultList.items
        }

        console.log(fileResultList)
    })
    

    
  </script>
  
  <main>
    <button class="back">
      <i class="mdi mdi-arrow-left" />
    </button>
  
    <div class="stage">
      <div class="folder-wrap level-current scrolling">
        {#each folders as item}
           <!-- content here -->
           <div class="tile folder">
                <Link to={`/${item['@expand'].child.id}`}>
                <i class="mdi mdi-folder" />
                </Link>
                <h3>{item['@expand'].child.name}</h3>
                <p>{item['@expand'].child.description}</p>
            </div>
            <!-- .tile.folder -->
        {/each}
        
        {#each  files as file}
        <div class="tile form">
          <i class="mdi mdi-file-document" />
          <h3>{file.name}</h3>
          <p>{file.description}</p>
        </div>
        {/each}
        <!-- .tile.form -->
      </div>
      <!-- .folder-wrap -->
    </div>
    <!-- .stage -->
  </main>
  
  <style>
    * {
      box-sizing: border-box;
    }
  
    body {
      font-family: source sans pro;
    }
    h3 {
      font-weight: 400;
      font-size: 16px;
    }
    p {
      font-size: 12px;
      color: #888;
    }
  
    .stage {
      max-width: 80%;
      margin: 60px 10%;
      position: relative;
    }
    .folder-wrap {
      display: flex;
      flex-wrap: wrap;
    }
    .folder-wrap::before {
      content: "Folder name";
      display: block;
      position: absolute;
      top: -40px;
    }
    .folder-wrap:first-child::before {
      /* content: "Home (top of file structure)"; */
      display: block;
      position: absolute;
      top: -40px;
    }
    .tile {
      border-radius: 3px;
      width: calc(20% - 17px);
      margin-bottom: 23px;
      text-align: center;
      border: 1px solid #eeeeee;
      transition: 0.2s all cubic-bezier(0.4, 0, 0.2, 1);
      position: relative;
      padding: 35px 16px 25px;
      margin-right: 17px;
      cursor: pointer;
    }
    .tile:hover {
      box-shadow: 0px 7px 5px -6px rgba(0, 0, 0, 0.12);
    }
    .tile i {
      color: #00a8ff;
      height: 55px;
      margin-bottom: 20px;
      font-size: 55px;
      display: block;
      line-height: 54px;
      cursor: pointer;
    }
    .tile i.mdi-file-document {
      color: #8fd9ff;
    }
  
    .back {
      font-size: 26px;
      border-radius: 50px;
      background: #00a8ff;
      border: 0;
      color: white;
      width: 60px;
      height: 60px;
      margin: 20px 20px 0;
      outline: none;
      cursor: pointer;
    }
  
    /* Transitioning */
    .folder-wrap {
      position: absolute;
      width: 100%;
      transition: 0.365s all cubic-bezier(0.4, 0, 0.2, 1);
      pointer-events: none;
      opacity: 0;
      top: 0;
    }
    .folder-wrap.level-up {
      transform: scale(1.2);
    }
    .folder-wrap.level-current {
      transform: scale(1);
      pointer-events: all;
      opacity: 1;
      position: relative;
      height: auto;
      overflow: visible;
    }
    .folder-wrap.level-down {
      transform: scale(0.8);
    }
  </style>
  