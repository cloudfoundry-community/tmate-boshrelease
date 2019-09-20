# Software Updates

- Update tmate to 2.3.0 (from 2.2.1)

# Improvements

- We now have a pipeline for releases!

- Property names no longer carry the `tmate.` prefix that was the
  hallmark of older BOSH releases from before BOSH supported
  per-job (then per-template) configuration.

  The upshot of that is that where you used to do this:

      properties:
        tmate:
          port: 2222

  You now do this:

      properties:
        port: 2222
