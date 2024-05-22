<template>
  <div class="mainContainer">
    <h1 style="text-transform: uppercase">{{ msg }}</h1>

    <hr>
    <br>

    <div class="inputs">
      <div id="slotInputDiv">
        <label for="slots">Number of Slots:</label>
        <input type="number" id="slots" min="1" max="5" v-model.number="numSlots" @change="generateGrid" />
      </div>

      <div id="memberInputDiv">
        <label for="members">Number of Members:</label>
        <input type="number" id="members" min="1" max="10" v-model.number="numMembers" @change="generateGrid" />
      </div>
    </div>
    
    <div class="grid-container">
      <div class="grid" :style="gridStyle">
        <div v-for="(row, rowIndex) in availabilityArray" :key="rowIndex" class="row">
          <div 
            v-for="(cell, colIndex) in row" 
            :key="colIndex" 
            :class="['cell', getColorClass(cell)]"
            @click="toggleState(rowIndex, colIndex)"
          >
          </div>
        </div>
      </div>

      <button id="imitateBackendButton" title="Imitate Backend Data" @click="generateRandomArray">GET DATA</button>
    </div>

    <br>
    <hr>

    <div id="footer">
      <div id="statesDiv">
        <h2 style="text-align: start">States</h2>

        <div class="stateColor">
          <div id="grayBox"></div>
          <div>(0) Default</div>
        </div>
        
        <div class="stateColor">
          <div id="greenBox"></div>
          <div>(1) Available</div>
        </div>
        
        <div class="stateColor">
          <div id="redBox"></div>
          <div>(2) Busy</div>
        </div>
      </div>

      <div id="reactiveDataDiv">
        <h2 style="text-align: start">Reactive Data</h2>
        <div v-for="(row, rowIndex) in availabilityArray" :key="rowIndex" :style="reactiveRowStyle" class="reactiveRow">
          <div v-for="(cell, cellIndex) in row" :key="cellIndex" class="reactiveCell">
            {{ cell }}
          </div>
        </div>
      </div>
    </div>

  </div>
  
</template>

<script>
import { reactive, toRefs, watch, computed } from 'vue';

export default {
  name: 'FreeSlotMatchComponent',
  props: {
    msg: String
  },

  setup() {
    const state = reactive({
      availabilityArray: [],
      numSlots: 3,
      numMembers: 5,
    });

    const generateGrid = () => {
       // Members Interval Check
      if (typeof state.numMembers != 'string' && state.numMembers < 1) 
        state.numMembers = 1;
      else if (typeof state.numMembers != 'string' && state.numMembers > 10) 
        state.numMembers = 10;

      // Slots Interval Check
      if (typeof state.numSlots != 'string' && state.numSlots < 1) 
        state.numSlots = 1;
      else if (typeof state.numSlots != 'string' && state.numSlots > 5) 
        state.numSlots = 5;
      else {
        // Rerender availability array by triggering reactive data 
        state.availabilityArray = Array.from({ length: state.numSlots }, () => 
          Array.from({ length: state.numMembers }, () => 0)
        );
      }

    };

    const getColorClass = (state) => {
      switch(state) {
        case 1:
          return 'green';
        case 2:
          return 'red';
        default:
          return 'grey';
      }
    };

    const toggleState = (rowIndex, colIndex) => {
      const currentState = state.availabilityArray[rowIndex][colIndex];
      const nextState = (currentState + 1) % 3;
      state.availabilityArray[rowIndex][colIndex] = nextState;
    };

    const generateRandomArray = () => {
      state.availabilityArray = Array.from({ length: state.numSlots }, () =>
        Array.from({ length: state.numMembers }, () => Math.floor(Math.random() * 3))
      );
    }

    const gridStyle = computed(() => {
      // TODO: Better width handling
      const gridSize = window.innerWidth > 768 ? 50 : 30;

      return {
        gridTemplateColumns: `repeat(${state.numMembers}, ${gridSize}px`
      };
    });

     const reactiveRowStyle = computed(() => {
      const gridSize = window.innerWidth > 768 ? 40 : 25;

      return {
        gridTemplateColumns: `repeat(${state.numMembers}, ${gridSize}px`
      };
    });

    // Watch for changes in numSlots and numMembers and regenerate grid
    watch(() => state.numSlots, generateGrid);
    watch(() => state.numMembers, generateGrid);

    generateGrid(); // Initialize the grid with default values
    generateRandomArray() // Imitate Receiving Backend Data initially

    return {
      ...toRefs(state),
      getColorClass,
      toggleState,
      generateRandomArray,
      generateGrid,
      gridStyle,
      reactiveRowStyle
    };
  }
};
</script>

<style scoped>

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
input {
  max-width: 40px;
  margin-right: 16px;
  margin-left: 4px;
}
label {
  align-content: center;
}

.mainContainerr {
  padding: 20px;
  border-radius: 8px;
  border: 2px solid #5E5E5E;
}

.inputs {
  display: flex;
  justify-content: center;
}

.grid {
  display: grid;
  gap: 10px;
  place-content: center;
  margin: 30px auto 0px;
  background-color: #000;
  width: fit-content;
  padding: 32px;
  border-radius: 8px;
}
.row {
  display: contents;
}
.cell {
  width: 50px;
  height: 50px;
  border: 1px solid #000;
  border-radius: 6px;
  cursor: pointer;
}
.grey {
  background-color: #5E5E5E;
}
.green {
  background-color: #30a91a;
}
.red {
  background-color:  #ad2c19;
}

#imitateBackendButton {
    margin: 10px;
    border-radius: 6px;
    border: 1px solid #000;
    padding: 4px 8px;
    background: #000;
    color: #fff;
    cursor: pointer;
}

.stateColor {
  display: flex; 
  align-items: center;
  margin-bottom: 8px;
}

#grayBox {
  width: 50px;
  height: 50px;
  background-color: #5E5E5E;
  border-radius: 8px;
  margin-right: 8px;
}

#greenBox {
  width: 50px;
  height: 50px;
  background-color: #30a91a;
  border-radius: 8px;
  margin-right: 8px;
}

#redBox {
  width: 50px;
  height: 50px;
  background-color: #ad2c19;
  border-radius: 8px;
  margin-right: 8px;
} 

.reactiveRow {
  display: grid;
  gap: 5px;
}

.reactiveCell {
  width: 30px;
  height: 30px;
  margin-bottom: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid #ccc;
  border-radius: 3px;
}

#footer {
  display: flex;
  justify-content: space-evenly;
}

/* RESPONSIVENESS */
/* Extra small devices (phones, less than 576px) */
@media (max-width: 575.98px) {

  .grid {
    display: grid;
    grid-template-columns: repeat(5, 30px);
    gap: 6px;
    margin: 8px auto 0px;
    width: fit-content;
    padding: 18px;
    border-radius: 6px;
  }
  .row {
    display: contents;
  }
  .cell {
    width: 30px;
    height: 30px;
    border: 1px solid #000;
    border-radius: 6px;
    cursor: pointer;
  }

  .inputs {
    display: grid;
    font-size: 16px;
  }

  #slotInputDiv {
    margin-bottom: 16px;
    display: flex;
    justify-content: space-between;
  }

  #slotInputDiv > input {
    font-size: 16px !important;
  }

  #memberInputsDiv {
    display: flex;
    justify-content: space-between;
  }

  #memberInputsDiv > input {
    font-size: 16px !important;
  }

  #footer {
    font-size: 12px;
    display: grid;
  }

  #grayBox, #greenBox, #redBox {
    width: 30px;
    height: 30px;
  }
}


/* Small devices (tablets, 576px and up) */
@media (min-width: 576px) and (max-width: 767.98px) {
  .mainContainer {
    border: 2px solid #5E5E5E;
    border-radius: 8px;
    padding: 40px;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(5, 30px);
    gap: 6px;
    margin: 8px auto 0px;
    width: fit-content;
    padding: 18px;
    border-radius: 6px;
  }
  .row {
    display: contents;
  }
  .cell {
    width: 30px;
    height: 30px;
    border: 1px solid #000;
    border-radius: 6px;
    cursor: pointer;
  }

  .inputs {
    display: grid;
    font-size: 16px;
  }

  #slotInputDiv {
    margin-bottom: 16px;
    display: flex;
    justify-content: space-between;
  }

  #slotInputDiv > input {
    font-size: 16px !important;
  }

  #memberInputsDiv {
    display: flex;
    justify-content: space-between;
  }

  #memberInputsDiv > input {
    font-size: 16px !important;
  }

  #footer {
    font-size: 12px;
    display: grid;
  }

  #grayBox, #greenBox, #redBox {
    width: 30px;
    height: 30px;
  }
}


/* Medium devices (tablets, 768px and up) */
@media (min-width: 768px) and (max-width: 991.98px) {
  .mainContainer {
    border: 2px solid #5E5E5E;
    border-radius: 8px;
    padding: 40px;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(5, 50px);
    gap: 10px;
    place-content: center;
    margin: 30px auto 0px;
    background-color: #000;
    width: fit-content;
    padding: 32px;
    border-radius: 8px;
  }
  .row {
    display: contents;
  }
  .cell {
    width: 50px;
    height: 50px;
    border: 1px solid #000;
    border-radius: 6px;
    cursor: pointer;
  }

  .inputs {
    display: flex;
  }

  #slotInputsDiv {
    margin-bottom: 6px;
    display: flex;
    justify-content: space-between;
  }

  #memberInputsDiv {
    display: flex;
    justify-content: space-between;
  }
}


/* Large devices (desktops, 992px and up) */
@media (min-width: 992px) and (max-width: 1199.98px) {
  .mainContainer {
    border: 2px solid #5E5E5E;
    border-radius: 8px;
    padding: 40px;
  }

  .grid {
    display: grid;
    gap: 10px;
    place-content: center;
    margin: 30px auto 0px;
    background-color: #000;
    width: fit-content;
    padding: 32px;
    border-radius: 8px;
  }
  .row {
    display: contents;
  }
  .cell {
    width: 50px;
    height: 50px;
    border: 1px solid #000;
    border-radius: 6px;
    cursor: pointer;
  }

  .inputs {
    display: flex;
  }

  #slotInputsDiv {
    margin-bottom: 6px;
    display: flex;
    justify-content: space-between;
  }

  #memberInputsDiv {
    display: flex;
    justify-content: space-between;
  }
}


/* Extra large devices (large desktops, 1200px and up) */
@media (min-width: 1200px) {

}
</style>
