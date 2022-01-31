<template>
  <div>
  <h1>Sorting Algorithm Visualiser</h1>
  <button class="active" @click="reload()">Reload</button>
  <button :class="{active: isActive}" @click="shuffleArray()" :disabled="dis">Shuffle</button>
  <button :class="{active: isActive}" @click="bubbleSort()" :disabled="dis">Bubble Sort</button>
  <button :class="{active: isActive}" @click="insertionSort()" :disabled="dis">Insertion Sort</button>
  <button :class="{active: isActive}" @click="selectionSort()" :disabled="dis">Selection Sort</button>
  <button :class="{active: isActive}" @click="radixBucketSort()" :disabled="dis">Radix Bucket Sort</button>
  <button :class="{active: isActive}" @click="flashSort()" :disabled="dis">Flash Sort</button>
  <button :class="{active: isActive}" @click="shellSort()" :disabled="dis">Shell Sort</button>

  <div class="container">
    <div class="bar" v-for="(number, index) in array" :key="index" :style="{height: (number/heightDiv) + 'px', backgroundColor: col}"></div>
  </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      array: [],
      size: document.documentElement.clientWidth*0.8-10,
      col: 'black',
      dis: false,
      isActive: true
    }
  },
  computed: {
    heightDiv: function() {
      let hDiv = this.size/(document.documentElement.clientHeight*(1-(150/document.documentElement.clientHeight)))
      return hDiv
    }
  },
  methods: {
    sorted() {
      this.col = 'green'
      this.isActive = true
      this.dis = false
    },
    begin() {
      this.isActive = false
      this.dis = true
    },
    reload() {
      location.reload();
      return false;
    },
    shuffleArray() {
    this.col = 'black'
    for (let i = this.array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [this.array[i], this.array[j]] = [this.array[j], this.array[i]];
    }
    },
    fillArray() {
      this.array = [];
      for (let i = 0; i < this.size; i++) {
        this.array.push(i)
      }
      this.shuffleArray()
    },
    getRndInteger(min,max) {
      return Math.floor(Math.random() * (max - min + 1)) + min; 
    },
    async bubbleSort(){
      this.begin()
      let len = this.array.length
      let checked;
      do {
        checked = false;
        for(let i =0;i<len;i++){
          if(this.array[i] > this.array[i+1]){
            let tmp = this.array[i];
            this.array[i] = this.array[i+1];
            this.array[i+1] = tmp;
            // sleep - to visualize / see the changes
            checked = true;
          }
        } await this.sleep()
      } while (checked)
      this.sorted()
    },
    async insertionSort() {
      this.begin()
      let n = this.array.length;
      for (let i = 1; i < n; i++) {
          // Choosing the first element in our unsorted subarray
          let current = this.array[i];
          // The last element of our sorted subarray
          let j = i-1; 
          while ((j > -1) && (current < this.array[j])) {
              this.array[j+1] = this.array[j];
              j--;
          }
          await this.sleep()
          this.array[j+1] = current;
        }
      this.sorted()
    },
    async selectionSort() { 
      this.begin()
      let n = this.array.length;
          
      for(let i = 0; i < n; i++) {
          // Finding the smallest number in the subarray
          await this.sleep()
          let min = i;
          for(let j = i+1; j < n; j++){
              if(this.array[j] < this.array[min]) {
                  min=j; 
              }
          }
          if (min != i) {
              // Swapping the elements
              let tmp = this.array[i]; 
              this.array[i] = this.array[min];
              this.array[min] = tmp; 
          }
        }
        this.sorted()
      },
    async radixBucketSort () {
     this.begin()
     var idx1, idx2, idx3, len1, len2, radix, radixKey;
     var radices = {}, buckets = {}, curr;
     var currLen, currBucket;
 
     len1 = this.array.length;
     len2 = 10;  // radix sort uses ten buckets
 
     // find the relevant radices to process for efficiency       
     for (idx1 = 0;idx1 < len1;idx1++) {
       radices[this.array[idx1].toString().length] = 0;
     }
 
     // loop for each radix. For each radix we put all the items
     // in buckets, and then pull them out of the buckets.
     for (radix in radices) {         
       // put each this.arrayay item in a bucket based on its radix value
       len1 = this.array.length;
       for (idx1 = 0;idx1 < len1;idx1++) {
         curr = this.array[idx1];
         // item length is used to find its current radix value
         currLen = curr.toString().length;
         // only put the item in a radix bucket if the item
         // key is as long as the radix
         if (currLen >= radix) {
           // radix starts from beginning of key, so need to
           // adjust to get redix values from start of stringified key
           radixKey = curr.toString()[currLen - radix];
           // create the bucket if it does not already exist
           if (!(radixKey in buckets)) {
             buckets[radixKey] = [];
           }
           // put the this.arrayay value in the bucket
           buckets[radixKey].push(curr);        
         } else {
           if (!('0' in buckets)) {
             buckets['0'] = [];
           }
           buckets['0'].push(curr);
         }
       }
       // for current radix, items are in buckets, now put them
       // back in the this.arrayay based on their buckets
       // this index moves us through the this.array as we insert items
       idx1 = 0;
       // go through all the buckets
       for (idx2 = 0;idx2 < len2;idx2++) {
         // only process buckets with items
         if (buckets[idx2] != null) {
           currBucket = buckets[idx2];
           // insert all bucket items into this.array
           len1 = currBucket.length;
           for (idx3 = 0;idx3 < len1;idx3++) {
             this.array[idx1++] = currBucket[idx3];
             await this.sleep();
           }
         }
       }
       buckets = {};
      }
      this.sorted()
    },
    async flashSort() {
    this.begin()
    var max = 0, min = this.array[0];
    var n = this.array.length;
    var m = ~~(0.45 * n);
    var l = new Array(m);
 
    for (var i = 1; i < n; ++i) {
        if (this.array[i] < min) {
            min = this.array[i];
        }
        if (this.array[i] > this.array[max]) {
            max = i;
        }
    }
 
     if (min === this.array[max]) {
        return this.array;
    }
 
    var c1 = (m - 1) / (this.array[max] - min);
 
 
    for (var k = 0; k < m; k++) {
        l[k] = 0;
    }
    for (var j = 0; j < n; ++j) {
        k = ~~(c1 * (this.array[j] - min));
        ++l[k];
    }
 
    for (var p = 1; p < m; ++p) {
        l[p] = l[p] + l[p - 1];
    }
 
    var hold = this.array[max];
    this.array[max] = this.array[0];
    this.array[0] = hold;
 
    //permutation
    var move = 0, t, flash;
    j = 0; 
    k = m - 1;
  
    while (move < (n - 1)) {
        while (j > (l[k] - 1)) {
            ++j;
            k = ~~(c1 * (this.array[j] - min));
        }
        if (k < 0) break;
        flash = this.array[j];
        while (j !== l[k]) {
            k = ~~(c1 * (flash - min));
            hold = this.array[t = --l[k]];
            this.array[t] = flash;
            flash = hold;
            ++move;
          await this.sleep()
        }
    }
 
    //insertion
    for (j = 1; j < n; j++) {
        hold = this.array[j];
         i = j - 1;
        while (i >= 0 && this.array[i] > hold) {
            this.array[i + 1] = this.array[i--];
        }
        this.array[i + 1] = hold;
      }
      this.sorted()
    },
  async shellSort() {
  this.begin()
	let n = this.array.length;

	for (let gap = Math.floor(n/2); gap > 0; gap = Math.floor(gap/2))	{
		for (let i = gap; i < n; i += 1)  {
			let temp = this.array[i];
			
			let j;
			for (j = i; j >= gap && this.array[j-gap] > temp; j-=gap)  {
				this.array[j] = this.array[j-gap];
			}

			this.array[j] = temp;

      await this.sleep()

		}
	}
  this.sorted()
  },
    sleep(e=1) {
      return new Promise((resolve) => setTimeout(resolve,e));
    }
  },
  created() {
    this.fillArray();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: center;
}


.bar {
  width: 1.2px;
  display: inline-block;
  margin: 1 0px;
}

button{

 display:inline-block;
 padding:0.2em 1.45em;
 margin:0.1em;
 border:0.15em solid #CCCCCC;
 box-sizing: border-box;
 text-decoration:none;
 font-family:'Segoe UI','Roboto',sans-serif;
 font-weight:400;
 color:#000000;
 background-color:#CCCCCC;
 text-align:center;
 position:relative;
}
button.active:hover{
 border-color:#7a7a7a;
}
button:active{
 background-color:#999999;
}
button:disabled{
  opacity: 0.45;
}
@media all and (max-width:30em){
 button{
 display:block;
 margin:0.2em auto;
 }
}

</style>
