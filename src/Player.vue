<script>
export default {
  // Gets us the provider property from the parent <my-canvas> component.
  inject: ['provider'],

  props: {
    // Start coordinates (percentage of canvas dimensions).
    x: {
      type: Number,
      default: 0
    },

    y: {
      type: Number,
      default: 0
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
    const sX = 64;
    const sY = 64;

    let x = this.x;
    let y = this.y;

    ctx.drawImage(image, sX, sY, tileW, tileH, x, y, canvW, canvH);
  }
}
</script>