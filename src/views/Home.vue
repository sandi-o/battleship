<template>
<v-container>
  <v-card>
    <v-card-title>
        {{whoseTurn}}
        <v-spacer></v-spacer>
        {{generalInfo}}
    </v-card-title>
    <div class='hidden-info'>
      <v-btn text color='primary' ref='start' :disabled="gameStart && !gameOver" @click="game">New Game</v-btn>     
    </div>
    <div class='container'>
        <div class='grid board' :disabled="!gameStart">
          <div v-for="(_,i) in Math.pow(gridSize,2)" :key="`p1-${i}`" :ref="`p1-${i}`" :data-id="i" data-board='p1'
            @dragstart="dragStart"
            @dragover="dragOver"
            @dragenter="dragEnter"
            @dragleave="dragLeave"
            @drop="dragDrop"
            @dragend="dragEnd"
            @click="fire"
          ></div>
        </div>
        <div class='grid board' :disabled="!gameStart || !p1ShipsMapped">
          <div v-for="(_,i) in Math.pow(gridSize,2)" :key="`p2-${i}`" :ref="`p2-${i}`" :data-id="i" data-board='p2'
            @dragstart="dragStart"
            @dragover="dragOver"
            @dragenter="dragEnter"
            @dragleave="dragLeave"
            @drop="dragDrop"
            @dragend="dragEnd"
            @click="fire"
          ></div>
        </div>
    </div>
    <div class='container' :disabled="!gameStart">
        <v-btn color='primary' :disabled="mapButton" ref='mapped' @click="mapped">{{currentPlayer}} Mapping Done</v-btn>  
        <v-spacer></v-spacer>
        <v-btn color='accent' :disabled="nextTurn" ref='turn' @click="turn">{{currentPlayer}} Turn Done</v-btn>  
    </div>
    <div class='container' :disabled="!gameStart">
       
      <div class='grid-display-p1 ml-15' ref="grid-display-p1">
        <div class='ship patrolboat-container-p1' 
          draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='x'
          data-player='p1'
        >
          <div id='patrolboat-0'></div>
        </div>
        <div class='ship destroyer-container-p1' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='x'
          data-player='p1'
        >
          <div id='destroyer-0'></div>
          <div id='destroyer-1'></div>
        </div>
        <div class='ship battleship-container-p1' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='x'
          data-player='p1'
        >
          <div id='battleship-0'></div>
          <div id='battleship-1'></div>
          <div id='battleship-2'></div>
        </div>
        <div class='ship carrier-container-p1' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='x'
          data-player='p1'
        >
          <div id='carrier-0'></div>
          <div id='carrier-1'></div>
          <div id='carrier-2'></div>
          <div id='carrier-3'></div>
        </div>
      </div>     
      <div class='grid-display-vertical-p1 mr-15'  ref="grid-display-vertical-p1">
        <div class='ship corvette-container-p1' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p1'
        >
          <div id='corvette-0'></div>
        </div>
        <div class='ship submarine-container-p1' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p1'
        >
          <div id='submarine-0'></div>
          <div id='submarine-1'></div>
        </div>
        <div class='ship warship-container-p1' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p1'
        >
          <div id='warship-0'></div>
          <div id='warship-1'></div>
          <div id='warship-2'></div>
        </div>
        <div class='ship cruiser-container-p1' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p1'
        >
          <div id='cruiser-0'></div>
          <div id='cruiser-1'></div>
          <div id='cruiser-2'></div>
          <div id='cruiser-3'></div>
        </div>
      </div>
      <!-- Player 2 -->
      <div class='grid-display-p2 ml-15' ref="grid-display-p2" :disabled="!p1ShipsMapped">
          <div class='ship patrolboat-container-p2' draggable="true"
            @dragstart="dragStart"
            @mousedown="mouseDown"
            data-position='x'
            data-player='p2'
          >
            <div id='patrolboat-0'></div>
          </div>
          <div class='ship destroyer-container-p2' draggable="true"
            @dragstart="dragStart"
            @mousedown="mouseDown"
            data-position='x'
            data-player='p2'
          >
            <div id='destroyer-0'></div>
            <div id='destroyer-1'></div>
          </div>
          <div class='ship battleship-container-p2' draggable="true"
            @dragstart="dragStart"
            @mousedown="mouseDown"
            data-position='x'
            data-player='p2'
          >
            <div id='battleship-0'></div>
            <div id='battleship-1'></div>
            <div id='battleship-2'></div>
          </div>
          <div class='ship carrier-container-p2' draggable="true"
            @dragstart="dragStart"
            @mousedown="mouseDown"
            data-position='x'
            data-player='p2'
          >
            <div id='carrier-0'></div>
            <div id='carrier-1'></div>
            <div id='carrier-2'></div>
            <div id='carrier-3'></div>
          </div>
      </div>
      <div class='grid-display-vertical-p2 mr-15' ref="grid-display-vertical-p2" :disabled="!p1ShipsMapped">
        <div class='ship corvette-container-p2' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p2'
        >
          <div id='corvette-0'></div>
        </div>
        <div class='ship submarine-container-p2' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p2'
        >
          <div id='submarine-0'></div>
          <div id='submarine-1'></div>
        </div>
        <div class='ship warship-container-p2' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p2'
        >
          <div id='warship-0'></div>
          <div id='warship-1'></div>
          <div id='warship-2'></div>
        </div>
        <div class='ship cruiser-container-p2' draggable="true"
          @dragstart="dragStart"
          @mousedown="mouseDown"
          data-position='y'
          data-player='p2'
        >
          <div id='cruiser-0'></div>
          <div id='cruiser-1'></div>
          <div id='cruiser-2'></div>
          <div id='cruiser-3'></div>
        </div>
      </div>
    </div>
 </v-card> 
</v-container>
</template>

<script>  
  export default {
    name: 'Home',
    data() {
      return {
        gridSize: 10,
        selectedShipNameWithIndex: null,
        selectedShipIndex: null,
        draggedShip: null,
        draggedShipPosition: null,
        draggedShipPlayer: null,
        draggedShipLen: null,
        gameStart: false,
        gameOver: false,
        currentPlayer:'',
        whoseTurn: 'Welcome to Battle Ship!',
        generalInfo: '',
        p2Health: {
          'patrolboat':{'lives': 1, 'damage': 0, 'sank':0},
          'destroyer':{'lives': 2, 'damage': 0, 'sank':0},        
          'battleship':{'lives': 3, 'damage': 0, 'sank':0},
          'carrier':{'lives': 4, 'damage': 0, 'sank':0},
          'corvette':{'lives': 1, 'damage': 0, 'sank':0},
          'submarine':{'lives': 2, 'damage': 0, 'sank':0},
          'warship':{'lives': 3, 'damage': 0, 'sank':0},
          'cruiser':{'lives': 4, 'damage': 0,'sank':0}
        },
        p1Health: { 
          'patrolboat':{'lives': 1, 'damage': 0,'sank':0},
          'destroyer':{'lives': 2, 'damage': 0,'sank':0},        
          'battleship':{'lives': 3, 'damage': 0,'sank':0},
          'carrier':{'lives': 4, 'damage': 0,'sank':0},
          'corvette':{'lives': 1, 'damage': 0,'sank':0},
          'submarine':{'lives': 2, 'damage': 0,'sank':0},
          'warship':{'lives': 3, 'damage': 0,'sank':0},
          'cruiser':{'lives': 4, 'damage': 0,'sank':0},
        },
        p1ShipsMapped: false,
        p2ShipsMapped: false,
        mapButton: true,
        nextTurn: true,
        targetedPlayerBoard: '',
      };
    },
  
    mounted() {
      
    },
    watch: {
      currentPlayer: function(newVal,oldVal) {        
        if(newVal =='p1') {
          if(!this.p1ShipsMapped){
            this.generalInfo = 'Map your ships Player 1'
          } else {
            this.generalInfo = 'P1 turn'
          }                 
        }
        if(newVal =='p2') {
          if(!this.p1ShipsMapped){
            this.generalInfo = 'Map your ships Player 2'
          } else {
             this.generalInfo = 'P2 turn'
          }          
        }  
        
        for(let x=0; x < Math.pow(this.gridSize,2); x++) {
          if(this.$refs[newVal+'-'+ x][0].classList.contains('taken')
            && this.$refs[newVal+'-'+ x][0].classList.contains('blend')){
            this.$refs[newVal+'-'+ x][0].classList.remove('blend')
          }

          if(oldVal != '' && this.$refs[oldVal+'-'+ x][0].classList.contains('taken')){
            this.$refs[oldVal+'-'+ x][0].classList.add('blend')
          }
        }
      }
    },
   
    methods: {
      mapped() {
        //hide all currentPlayer ships
        //change currentPlayer
        for(let x=0; x < Math.pow(this.gridSize,2); x++) {
          if(this.$refs[this.currentPlayer+'-'+ x][0].classList.contains('taken'))
            this.$refs[this.currentPlayer+'-'+ x][0].classList.add('blend')
        }
        
        this[this.currentPlayer +'ShipsMapped'] = true;
        
        if(this.currentPlayer == 'p1'){
          this.currentPlayer = 'p2'    
          this.mapButton = true
        }    
        //check if both players successfully mapped their ship
        if(this.p1ShipsMapped && this.p2ShipsMapped){
            this.mapButton = true
            this.nextTurn = false
            this.currentPlayer = 'p1'                       
        }

      },
      mouseDown(e) {
        this.selectedShipNameWithIndex = e.target.id         
      },
      dragStart(e) {
        this.draggedShip = e.target
        this.draggedShipPlayer = e.target.dataset.player
        this.draggedShipPosition = e.target.dataset.position
        this.draggedShipLen = e.target.childNodes.length
      },
      dragOver(e) {
        e.preventDefault()
      },
      dragEnter(e) {
        e.preventDefault()
      },
      dragLeave() {
        console.log('drag leave')
      },
      dragDrop(e) {
        let dataSetId = parseInt(e.target.dataset.id)
        //gets the last div inside the (div)ship class
        let draggedShipRear = this.draggedShip.lastChild.id
        //slices the div id e.g. destroyer-2 returns destroyer
        let shipRearClassName = draggedShipRear.slice(0,-2)
        
        //sub string the div id e.g destroyer-2 returns 2
        let shipRearId = parseInt(draggedShipRear.substr(-1))
        //the id in the board where the ships rear will be placed
        //where e.target.dataset.id is the id of the board
        let shipRearBoardId = shipRearId + dataSetId

        const xNotAllowed = [0,10,20,30,40,50,60,70,80,90,1,11,21,31,41,51,61,71,81,91,2,12,22,32,42,52,62,72,82,92,3,13,23,33,43,53,63,73,83,93]
        const yNotAllowed = [99,98,97,96,95,94,93,92,91,90,89,88,87,86,85,84,83,82,81,80,79,78,77,76,75,74,73,72,71,70,69,68,67,66,65,64,63,62,61,60]
        
        let newXNotAllowed = xNotAllowed.splice(0,10*shipRearId)
        let newYNotAllowed= yNotAllowed.splice(0,10*shipRearId)

        //where selectedShipNameWithIndex is the part (index) of the ship that is clicked 
        this.selectedShipIndex  = parseInt(this.selectedShipNameWithIndex.substr(-1))  
        //less the selectedShipIndex to identify the last board id of the rear ship 
        shipRearBoardId -= this.selectedShipIndex
        let overLapping = false
        //horizonal
        if(this.draggedShipPosition == 'x' && !newXNotAllowed.includes(shipRearBoardId)) {
          //check if the square that will be occupied is not taken by other ships
          for(let x=0; x < this.draggedShipLen; x++) {
            let squareId = this.$refs[this.draggedShipPlayer+'-'+(dataSetId - this.selectedShipIndex + x)][0];
            if(squareId.classList.contains('taken')){
              overLapping = true
              break;
            }
          }

          if(!overLapping){
            for(let x=0; x < this.draggedShipLen; x++) {
              let squareId = this.$refs[this.draggedShipPlayer+'-'+(dataSetId - this.selectedShipIndex + x)][0];
            
              squareId.classList.add('taken',shipRearClassName)            
            }
            this.$refs['grid-display-'+this.draggedShipPlayer].removeChild(this.draggedShip)   
          }          
        //vertical
        } else if(this.draggedShipPosition == 'y' && !newYNotAllowed.includes(shipRearBoardId)) {
          //check if the square that will be occupied is not taken by other ships
          for(let y=0; y < this.draggedShipLen; y++) {
            let squareId = this.$refs[this.draggedShipPlayer+'-'+(dataSetId - this.selectedShipIndex + this.gridSize * y)][0];
            if(squareId.classList.contains('taken')){
              overLapping = true
              break;
            }
          }

          if(!overLapping){
            for(let y=0; y < this.draggedShipLen; y++) {            
              this.$refs[this.draggedShipPlayer+'-'+(dataSetId - this.selectedShipIndex + this.gridSize * y)][0].classList.add('taken',shipRearClassName)
            }
            this.$refs['grid-display-vertical-'+this.draggedShipPlayer].removeChild(this.draggedShip) 
          }  
        } else return

        //enable map button - check if the ship dock is empty
        if(!this.$refs['grid-display-vertical-'+this.draggedShipPlayer].childNodes.length 
          && !this.$refs['grid-display-'+this.draggedShipPlayer].childNodes.length) {
          this.mapButton = false;
        }

      },
      dragEnd() {

      },
      game() {
        this.gameStart = true
        if(this.gameOver) {
          location.reload()
        }
          
        if(this.currentPlayer == '')
          this.currentPlayer = 'p1'          
      },
      turn() {
        this.currentPlayer = this.targetedPlayerBoard
      },
      fire(e) {               
        let player = this.targetedPlayerBoard = e.target.dataset.board
        //check if click square contains the following class
        let squareClassList = this.$refs[player+'-'+e.target.dataset.id][0].classList        
        //prevent the player from clicking the other players board
        //prevemt the player from clicking the square that is already has a class of miss or boom 
        if(player == this.currentPlayer || (squareClassList.contains('miss') || squareClassList.contains('boom'))) {
          console.log('invalid board');
          return;
        } else {
          
          if(!squareClassList.contains('boom')){
            //check if the clicked square contains a ship class. increase the the damage of the ship.
            for(let ship in this[player +'Health']) {
              if(squareClassList.contains(ship)) this[player +'Health'][ship]['damage']++;
            }
          }
          
          if(squareClassList.contains('taken')) {
            squareClassList.add('boom')
            this.whoseTurn = 'Direct Hit!'
          } else {
            squareClassList.add('miss')
            this.whoseTurn = 'Miss.'
          }
        }
        
        if(this.winner(this.targetedPlayerBoard)){
          this.whoseTurn = 'Game Over'
          this.generalInfo = this.currentPlayer +' Wins!'
          this.gameOver = true;
          this.gameStart = false
        }

        //this.game()
      },
      winner(player) {
        let status = true
        for(const [ship,stats] of Object.entries(this[player +'Health'])) {     
          if(stats['lives'] != stats['damage'] && status){
            status = false;
          }
          //check if the ship has been sank
          if(stats['lives'] == stats['damage'] && !stats['sank']){
              this.whoseTurn =  'you sank the enemy\'s ' + ship
              stats['sank'] = 1
          }
        }

        return status;
      }

    },
  }
</script>
<style scoped>
.container {
  display: flex;
  justify-content: center;
  width: 100%;
}

.board {
  margin: 2vmin;
  display: grid;
  background-color: #2962FF;
  grid-template-rows: repeat(10,4.6vmin);
  grid-template-columns: repeat(10,4.6vmin);
}

.grid div {border: solid 1px;}

.grid-display-p1 {
  width: 200px;
  height: 400px;
  background-color: #82B1FF;
}

.grid-display-vertical-p1 { 
  width: 200px;
  height: 400px;
  background-color: #FF8A80; 
  display: flex;
}

.grid-display-p2 {
  width: 200px;
  height: 400px;
  background-color: #82B1FF;
}

.grid-display-vertical-p2 { 
  width: 200px;
  height: 400px;
  background-color: #FF8A80; 
  display: flex;
}

.patrolboat-container-p1, 
.patrolboat-container-p2 {
  width: 40px;
  height: 40px;
  background-color: #607D8B;
  display: flex;
}

.destroyer-container-p1,
.destroyer-container-p2 {
  width: 80px;
  height: 40px;
  background-color: #795548;
  display: flex;
}

.battleship-container-p1,
.battleship-container-p2 {
  width: 120px;
  height: 40px;
  background-color: #009688;
  display: flex;
}

.carrier-container-p1,
.carrier-container-p2 {
  width: 160px;
  height: 40px;
  background-color: #FF9800;
  display: flex;
}

.corvette-container-p1,
.corvette-container-p2 {
  width: 40px;
  height: 40px;
  background-color: #FFEB3B;
}

.submarine-container-p1,
.submarine-container-p2 {
  width: 40px;
  height: 80px;
  background-color: #4CAF50;
  display: flex;
  flex-wrap: wrap;
}

.warship-container-p1,
.warship-container-p2 {
  width: 40px;
  height: 120px;
  background-color: #9C27B0;
  display: flex;
  flex-wrap: wrap;
}

.cruiser-container-p1,
.cruiser-container-p2 {
  width: 40px;
  height: 160px;
  background-color: #00BCD4;
  display: flex;
  flex-wrap: wrap;
}

.ship div {
  width: 40px;
  height: 40px;
  border: solid 1px;
}

.patrolboat {background-color: #607D8B;}
.destroyer { background-color: #795548;}
.battleship {background-color: #009688;}
.carrier {background-color: #FF9800;}
.corvette {background-color: #FFEB3B;}
.submarine {background-color: #4CAF50;}
.warship {background-color: #9C27B0;}
.cruiser {background-color: #00BCD4;}

.blend {
  background-color: #2962FF !important;  
}

.boom { background-color: black !important;}
.miss { background-color: whitesmoke;}

div[disabled] {
  pointer-events: none;
  opacity: 0.10;
}

</style>