<script lang="ts">
  import { invalidate } from '$app/navigation';
  import { onMount, type Snippet } from 'svelte';
  import type { Session, SupabaseClient } from '@supabase/supabase-js';

  interface Props {
    session: Session | null;
    supabase: SupabaseClient;
    children: Snippet;
  }

  let { session, supabase, children }: Props = $props();

  onMount(() => {
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
      if (newSession?.expires_at !== session?.expires_at) {
        invalidate('supabase:auth');
      }
    });

    return () => data.subscription.unsubscribe();
  });

</script>

{@render children()}
