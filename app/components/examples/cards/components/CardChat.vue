<script setup lang="ts">
const input = ref('')
const inputLength = computed(() => input.value.trim().length)
const users = ref([
  {
    name: 'Olivia Martin',
    email: 'm@example.com',
    avatar: '/avatars/01.png',
  },
  {
    name: 'Isabella Nguyen',
    email: 'isabella.nguyen@email.com',
    avatar: '/avatars/03.png',
  },
  {
    name: 'Emma Wilson',
    email: 'emma@example.com',
    avatar: '/avatars/05.png',
  },
  {
    name: 'Jackson Lee',
    email: 'lee@example.com',
    avatar: '/avatars/02.png',
  },
  {
    name: 'William Kim',
    email: 'will@email.com',
    avatar: '/avatars/04.png',
  },
])

type User = (typeof users.value)[number]

const messages = ref([
  { role: 'agent', content: 'Hi, how can I help you today?' },
  { role: 'user', content: 'Hey, I\'m having trouble with my account.' },
  { role: 'agent', content: 'What seems to be the problem?' },
  { role: 'user', content: 'I can\'t log in.' },
])

const open = ref(false)
const selectedUsers = ref<User[]>([])
</script>

<template>
  <Card>
    <CardHeader class="flex flex-row items-center justify-between">
      <div class="flex items-center space-x-4">
        <Avatar>
          <AvatarImage src="/avatars/01.png" alt="Image" />
          <AvatarFallback>OM</AvatarFallback>
        </Avatar>
        <div>
          <p class="text-sm font-medium leading-none">
            Sofia Davis
          </p>
          <p class="text-sm text-muted-foreground">
            m@example.com
          </p>
        </div>
      </div>
      <TooltipProvider>
        <Tooltip :delay-duration="200">
          <TooltipTrigger as-child>
            <Button
              variant="outline"
              class="flex items-center justify-center rounded-full p-2.5"
              @click="open = true"
            >
              <Icon name="lucide:plus" class="h-4 w-4" />
            </Button>
          </TooltipTrigger>
          <TooltipContent :side-offset="10">
            New message
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>
    </CardHeader>
    <CardContent>
      <div class="space-y-4">
        <div
          v-for="(message, index) in messages"
          :key="index"
          :class="cn(
            'flex w-max max-w-[75%] flex-col gap-2 rounded-lg px-3 py-2 text-sm',
            message.role === 'user' ? 'ml-auto bg-primary text-primary-foreground' : 'bg-muted',
          )"
        >
          {{ message.content }}
        </div>
      </div>
    </CardContent>
    <CardFooter>
      <form
        class="w-full flex items-center space-x-2"
        @submit.prevent="() => {
          if (inputLength === 0) return
          messages.push({
            role: 'user',
            content: input,
          })
        }"
      >
        <Input v-model="input" placeholder="Type a message..." class="flex-1" />
        <Button class="flex items-center justify-center p-2.5" :disabled="inputLength === 0">
          <Icon name="lucide:send" class="h-4 w-4" />
          <span class="sr-only">Send</span>
        </Button>
      </form>
    </CardFooter>
  </Card>

  <Dialog v-model:open="open">
    <DialogContent class="gap-0 p-0 outline-none">
      <DialogHeader class="px-4 pb-4 pt-5">
        <DialogTitle>New message</DialogTitle>
        <DialogDescription>
          Invite a user to this thread. This will create a new group
          message.
        </DialogDescription>
      </DialogHeader>
      <Command
        class="overflow-hidden border-t rounded-t-none"
        :filter-function="(list: any[], search) => list.filter(l => l.name.toLowerCase().includes(search.toLowerCase()))"
      >
        <CommandInput placeholder="Search user..." />
        <CommandList>
          <CommandEmpty>No users found.</CommandEmpty>
          <CommandGroup class="p-2">
            <CommandItem
              v-for="user in users"
              :key="user.email"
              :value="user"
              class="flex items-center px-2"
              @select="() => {
                const index = selectedUsers.findIndex(u => u === user)
                if (index !== -1) {
                  selectedUsers.splice(index, 1)
                }
                else {
                  selectedUsers.push(user)
                }
              }"
            >
              <Avatar>
                <AvatarImage :src="user.avatar" alt="Image" />
                <AvatarFallback>{{ user.name[0] }}</AvatarFallback>
              </Avatar>
              <div class="ml-2">
                <p class="text-sm font-medium leading-none">
                  {{ user.name }}
                </p>
                <p class="text-sm text-muted-foreground">
                  {{ user.email }}
                </p>
              </div>
              <Icon v-if="selectedUsers.includes(user)" name="lucide:check" class="ml-auto h-5 w-5 flex text-primary" />
            </CommandItem>
          </CommandGroup>
        </CommandList>
      </Command>
      <DialogFooter class="flex items-center border-t p-4 sm:justify-between">
        <div v-if="selectedUsers.length > 0" class="flex overflow-hidden -space-x-2">
          <Avatar
            v-for="user in selectedUsers"
            :key="user.email"
            class="inline-block border-2 border-background"
          >
            <AvatarImage :src="user.avatar" />
            <AvatarFallback>{{ user.name[0] }}</AvatarFallback>
          </Avatar>
        </div>

        <p v-else class="text-sm text-muted-foreground">
          Select users to add to this thread.
        </p>

        <Button
          :disabled="selectedUsers.length < 2"
          @click="open = false"
        >
          Continue
        </Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>
