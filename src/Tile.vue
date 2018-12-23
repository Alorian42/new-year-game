<script>
export default {
  // Gets us the provider property from the parent <my-canvas> component.
  inject: ['provider'],

  props: {
    // Start coordinates (percentage of canvas dimensions).
    map: {
      type: Array,
      default: []
    },

    frame: {
      type: Number,
      default: 0
    },

  },

  data () {
    return {
        
    }
  },

  computed: {
    
  },
  methods: {
    renderer: function() {
        // Since the parent canvas has to mount first, it's *possible* that the context may not be
        // injected by the time this render function runs the first time.
        if (!this.provider.context || !this.map) {
            return;
        }
        
        const ctx = this.provider.context;
        const image = this.provider.image;
        const frame = this.frame;
        const tileW = 64;
        const tileH = 64;
        const canvW = 64;
        const canvH = 64;
        let map = this.map;

        for(let y = 0; y < map.length; y++) {
            for (let x = 0; x < map[y].length; x++) {
                let sX = 0;
                let sY = 0;
                switch(map[y][x]) {
                    case 0:
                        sX = 0;
                        sY = 0;
                        break;
                    case 1:
                        sX = 64;
                        sY = 0;
                        break;
                    case 2: 
                        sX = 128;
                        sY = 0;
                        break;
                    default:
                        sX = 0;
                        sY = 0;
                        break;
                }
                ctx.drawImage(image, sX, sY, tileW, tileH, x * canvW, y * canvH, canvW, canvH);
            }
        }
      }
  },
  render () {
    this.renderer();
  }
}
</script>