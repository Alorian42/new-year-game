<script>
export default {
  // Gets us the provider property from the parent <my-canvas> component.
  inject: ['provider'],

  props: {
    frame: {
      type: Number,
      default: 0
    },

    wisps: {
      type: Array,
      default: []
    }

  },

  data () {
    return {
        
    }
  },

  computed: {
    
  },

  render () {
    // Since the parent canvas has to mount first, it's *possible* that the context may not be
    // injected by the time this render function runs the first time.
    if (!this.provider.context) {
        return;
    }
    
    const ctx = this.provider.context;
    const image = this.provider.image;
    const frame = this.frame;
    const tileW = 64;
    const tileH = 64;
    const canvW = 64;
    const canvH = 64;
    const sX = 0;
    const sY = [64, 128, 192, 256];

    this.wisps.forEach(element => {
      let x = element.x;
      let y = element.y;

      ctx.drawImage(image, sX, sY[element.animationFrame], tileW, tileH, x, y, canvW, canvH);
    });
  }
}
</script>