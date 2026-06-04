npx create-next-app@latest next-gen-dashboard \
  --typescript --tailwind --eslint --app --yes

cd next-gen-dashboard
npm install framer-motion lucide-react @supabase/supabase-js @supabase/ssr..

NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key

create table courses (
  id uuid primary key default uuid_generate_v4(),
  title text not null,
  progress integer not null default 0,
  icon_name text not null,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

insert into courses (title, progress, icon_name)
values 
  ('Advanced React Patterns', 75, 'Code2'),
  ('UI/UX Mastery', 45, 'Palette'),
  ('TypeScript Deep Dive', 90, 'FileCode'),
  ('3D Web with Three.js', 30, 'Cube');

import { createClient } from '@/lib/supabase/server';
import { HeroTile } from '@/components/HeroTile';
import { CourseGrid } from '@/components/CourseGrid';
import { ActivityTile } from '@/components/ActivityTile';
import { Sidebar } from '@/components/Sidebar';

export default async function Dashboard() {
  const supabase = await createClient();
  const { data: courses, error } = await supabase
    .from('courses')
    .select('*')
    .order('created_at', { ascending: false });

  if (error) {
    console.error(error);
  }

  return (
    <div className="flex h-screen bg-zinc-950 text-white overflow-hidden">
      <Sidebar />
      
      <main className="flex-1 overflow-auto p-6 md:p-8">
        <div className="max-w-7xl mx-auto">
          <HeroTile name="Alex Chen" streak={7} />

          <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-12 gap-6 mt-8">
            <CourseGrid courses={courses || []} />
            <ActivityTile />
          </div>
        </div>
      </main>
    </div>
  );
}









